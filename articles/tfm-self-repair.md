---
layout: post
title: "Do tabular foundation models repair themselves?"
desc: "Balef et al. pose the right dichotomy — redundancy or repair — but adopt a criterion both alternatives satisfy. Measuring the direct effect resolves it."
mathjax: true
permalink: /articles/tfm-self-repair/
date: 2026-08-10
---

**Abstract.** Ablation is the workhorse of mechanistic interpretability: we delete a component, measure the damage, and read the damage as the component's importance. Self-repair breaks this inference, because downstream components can compensate for the deleted one. Self-repair has been quantified in language models. A recent study of tabular foundation models (TFMs) reports that it is present there as well. However, the criterion it adopts does not separate self-repair from mere redundancy. In this paper we measure the quantity that does, i.e., the direct effect of a layer, using a causal intervention known as path patching. We evaluate four state-of-the-art TFMs on 15 binary classification tasks and find that the compensation effect is approximately zero throughout, so the recovery is passive redundancy rather than active repair. Our experiments on real-world tabular benchmarks validate the null across three independent robustness checks.

---

## Table of contents
{:.no_toc}

* TOC
{:toc}

---

## 1. Introduction
{: #introduction}

Mechanistic interpretability rests on a simple inference. We ablate a component, we observe how much the output degrades, and we attribute the degradation to the component's function. The inference is only valid if the rest of the network is a passive spectator. Self-repair is the phenomenon that breaks this assumption: after a component is removed, downstream components change what they compute and restore part of the lost output. The consequence is that ablation-based importance systematically underestimates the components that matter most. This has been documented repeatedly in language models [[1](#ref-1), [2](#ref-2), [3](#ref-3), [4](#ref-4)].

**Tabular foundation models.** Tabular foundation models are transformers that solve a supervised tabular task by in-context learning: a single forward pass consumes the entire table — the labelled support rows together with the unlabelled query rows — and emits predictions for the queries, with no gradient step and no task-specific fitting. One property matters for everything that follows: the input table remains in context for the whole forward pass.

**The gap.** Self-repair has been quantified only in language models. Balef et al. [[5](#ref-5)] give the first mechanistic study of layerwise dynamics in TFMs and report that it occurs in the middle and late layers of most of them. However, their criterion is the shape of a trajectory: after skipping a layer they decode at every subsequent depth and read a drop followed by a recovery of ROC AUC as evidence of repair. That reading presupposes a mechanism we call *active repair*, in which the downstream layers respond to the missing write. A second mechanism, *passive redundancy*, produces the same trajectory: if the information written by the skipped layer is *duplicated* elsewhere, the downstream layers recover the output without changing what they write.

**Our goal.** We study whether TFMs exhibit self-repair in the causal sense in which it is defined for language models. Balef et al. explicitly pose the right dichotomy — redundancy or repair — but adopt a criterion that both alternatives satisfy. We resolve the dichotomy with a quantity their design does not contain: *the direct effect* [[1](#ref-1)], measured via path patching [[2](#ref-2)]. In summary, we make the following contributions relative to [[5](#ref-5)].

- We show that the trajectory criterion cannot establish self-repair: no measurement taken on the ablated forward pass alone separates active repair from passive redundancy.
- We propose a measurement for TFMs built from three ingredients: (i) the direct effect via path patching; (ii) a logit-based measure in place of ROC AUC; and (iii) resample ablation in place of zero ablation.
- Based on the proposed measurement and on results for four TFMs and 15 tasks, we draw a different conclusion from that of [[5](#ref-5)]: the performance recovery reported for TFMs is passive redundancy.

## 2. Self-repair: definition and prior quantification
{: #definition}

**Intuition.** The intuition behind self-repair is that a network is not a fixed pipeline but a system that reacts to its own damage. When a component is removed, the components downstream of it see a different residual stream, and they may respond by writing something different from what they would have written otherwise. Self-repair is the case in which that response pushes the output back towards its undamaged value. Note that the definition is a statement about the downstream *reaction*, not about the output: an output that barely moves is compatible with a strong reaction (e.g., active repair) and with no reaction at all (e.g., passive redundancy).

**Formalism.** The network can be viewed as a causal model [[6](#ref-6)] in which \\(A\\) denotes the component under study (e.g., a layer or an attention head), \\(B\\) denotes everything downstream of it, and \\(y\\) denotes a scalar read off the final decoder that measures how strongly the model favours the correct class; we fix the choice in Section 4. We refer to the unablated forward pass as the *clean* pass — the **Original** panel of Figure 1 — and to the value a component takes in it as its *clean* value. Let \\(\tilde{a}\\) be the value we substitute for \\(A\\)'s output, written \\(a'\\) in the figure. We define three effects, illustrated in the remaining three panels, which differ only in what we allow to react.

- The **total effect** \\(TE\\) is the change in \\(y\\) when we set \\(A = \tilde{a}\\) and let \\(B\\) react freely.
- The **direct effect** \\(DE\\) is the change in \\(y\\) when we set \\(A = \tilde{a}\\) and simultaneously freeze \\(B\\) at its clean values, so that only the path \\(A \to y\\) carries the intervention.
- The **indirect effect** \\(IE\\) is the change in \\(y\\) when we leave \\(A\\) clean and set \\(B\\) to the values it would take under the intervention, so that only the path \\(A \to B \to y\\) carries it.

![Total, direct, and indirect effects](/assets/img/tfm-self-repair/hydra-te-de-ie.png)
*Figure 1: **Original** is the clean pass, in which \\(a\\) and \\(b\\) take their clean values. The three effects differ only in which nodes the intervention reaches: \\(do(A = a')\\) substitutes \\(A\\)'s output, and \\(do(B = b)\\) pins \\(B\\) to its clean value so that the intervention cannot travel through it. Taken from [[1](#ref-1)].*

The three are related by \\(TE = DE + IE\\). Following [[1](#ref-1)] we define the **compensation effect**

$$CE = DE - TE = -IE ,$$

and we say that a component *self-repairs* if \\(CE > 0\\), i.e., if the damage that reaches the output is smaller than the damage the component's own contribution would predict. Note that \\(CE < 0\\) means the downstream reaction amplifies the loss and \\(CE = 0\\) means the downstream layers are passive.

**Why both \\(TE\\) and \\(DE\\) are needed.** It is easy to see that neither effect alone identifies self-repair. Consider a toy network with two components, \\(A\\) and \\(B\\), writing values \\(a\\) and \\(b\\) into a shared stream read at the output as \\(y = a + b\\), and consider three worlds. All three produce the same clean output, \\(y = 1\\), so nothing distinguishes them until we intervene; the intervention is \\(do(A = 0)\\).

| world | \\(A\\) writes | \\(B\\)'s rule | \\(DE\\) | \\(TE\\) | \\(CE\\) |
|---|---|---|---|---|---|
| ① redundant | \\(0\\) | \\(b = 1\\) always | \\(0\\) | \\(0\\) | \\(0\\) |
| ② repaired | \\(1\\) | \\(b = 1 - a\\) | \\(1\\) | \\(0\\) | \\(1\\) |
| ③ load-bearing | \\(1\\) | \\(b = 0\\) always | \\(1\\) | \\(1\\) | \\(0\\) |

*Table 1: three worlds that are indistinguishable at the clean output. Only \\(CE\\) takes a different value in each of the three, so only \\(CE\\) identifies the repaired one.*

The total effect is \\(0\\) in both ① and ②, and therefore cannot separate them; the direct effect is \\(1\\) in both ② and ③, and therefore cannot separate those. Only the gap separates all three, since \\(CE\\) is non-zero in ② and zero in ① and ③. We use this table as the reading key for the remainder of the paper.

**Quantification in language models.** McGrath et al. [[1](#ref-1)] evaluate a 7B-parameter Chinchilla model on 1209 factual-recall prompts, ablating one attention layer at a time. They obtain \\(DE\\) by unembedding the layer's own output directly, and \\(TE\\) by ablating the layer and reading the final logits. Figure 2 shows their central result: the point cloud of \\((DE, TE)\\) pairs sits predominantly *below* the diagonal \\(y = x\\), i.e., \\(TE < DE\\) for a large fraction of (layer, prompt) pairs. This is the decoupling that the naive ablation account does not predict, and it is the empirical signature of \\(CE > 0\\).

![Hydra scatter: TE versus DE](/assets/img/tfm-self-repair/hydra-ref-scatter.png)
*Figure 2: direct effect against total effect in a language model. One point is one (layer, prompt) pair, coloured by layer depth. Mass below the diagonal is the signature of self-repair. Taken from Figure 2c of [[1](#ref-1)].*

Moreover, the compensation is proportional. Regressing \\(CE\\) on \\(DE\\) across prompts, layer by layer, they find a tight linear relation in the middle and late layers, peaking at layer 23 with \\(R^2 = 0.92\\) and slope \\(0.69\\) (Figures 4b and 4d of [[1](#ref-1)]).[^hydrafit] We adopt both as our reference, since a scattered relation would indicate coincidence rather than a mechanism.

[^hydrafit]: The two numbers carry distinct information. The \\(R^2\\) says the compensation is systematic, i.e., how much is lost predicts how much is restored. The slope says it is partial, i.e., about 70% of the direct contribution is restored and the remaining 30% survives to the output.

## 3. Self-repair in TFMs: the prior claim and what it identifies
{: #prior-claim}

We proceed in four steps: (i) what Balef et al. do and what they conclude; (ii) the main argument, that their criterion cannot identify self-repair, which is a matter of logic and not of measurement; (iii) two measurement choices that make the phenomenon harder to see; and (iv) what we do not dispute.

### 3.1 What Balef et al. [[5](#ref-5)] do
{: #prior-experiment}

They study six state-of-the-art TFMs. Their self-repair experiment has four parts.

- **Instrument** — the *tabular logit lens*. A TFM's native decoder expects the representation of the final layer, so they train a separate decoder for every depth and use it to read out intermediate performance.
- **Intervention** — *layer skipping*. They remove one layer and apply the lens at every subsequent depth, which gives a trajectory of decoded performance.
- **Criterion** — "if the TFM can recover from dropping a layer, it has learned to self-repair, and layers overlap in functionality".
- **Conclusion** — "self-repair generally occurs after layer ablations, except for the first layer".

Figure 3 shows our reproduction of this experiment. Skipping an early layer produces a drop that persists to the output, whereas skipping a middle or late layer produces a drop that closes well before the final layer.

![The layer-skipping experiment, schematic and real](/assets/img/tfm-self-repair/exp6-transition.png)
*Figure 3: the layer-skipping experiment. **Left**: one ablated layer, for illustration — the black baseline is the unablated trajectory, the red cross marks the skipped layer, and the decoded performance drops at the next depth and then recovers. **Right**: the same experiment on LimiX-2M over 15 tasks, with every layer ablated in turn; one coloured curve is one skipped layer, i.e., one instance of the left panel. Early layers do not recover, middle and late layers do. The other three models are in Appendix B; all four agree with what the paper reports, including the two models it does not study.*

**The authors pose the dichotomy themselves.** They admit, in their own words, that it is unclear whether the robustness to layer ablation comes from self-repair or from layer redundancy, since performance has been measured only at the final layer. Their diagnosis is that too few depths are measured and they remedy it by measuring all of them. In contrast, we argue below that the number of depths is not what the dichotomy turns on.

### 3.2 The criterion cannot identify self-repair
{: #identifiability}


We now state the main argument, which is independent of how performance is measured. Let the network have \\(L\\) layers and let \\(m\\) be the layer that is skipped. For any layer \\(\ell\\), write \\(a_\ell\\) for the value it writes into the residual stream in the clean pass and \\(\hat{a}_\ell\\) for what it writes in the ablated pass.

- Under **active repair**, the downstream layers respond to the missing write, so $$\hat{a}_\ell \neq a_\ell$$ for $$\ell > m$$, and the recovery is produced by that response.
- Under **passive redundancy**, the downstream layers write exactly what they always write, $$\hat{a}_\ell = a_\ell$$, and the recovery occurs because the information carried by \\(a_m\\) is duplicated in the residual stream and in the input table, which remains in context.

Both produce a drop at depth \\(m+1\\), where \\(a_m\\) is simply absent from the residual stream, and both produce a recovery by depth \\(L\\). The two mechanisms therefore draw the same curve, and every curve in Figure 3 admits both readings. Therefore the trajectory criterion cannot decide between them. The reason is structural: repair is by definition a claim about the downstream *reaction*, and to establish a reaction one needs a counterfactual in which the reaction is prevented. Measuring at more depths samples the same ablated forward pass more finely; it never constructs a pass in which the downstream writes are pinned to their clean values. In other words, the design contains no term in which downstream reaction is blocked, and a measurement without such a term cannot identify the reaction's contribution. Note that this is an identifiability argument and not a statistical one — more models, more datasets, or more depths do not help.

### 3.3 Two measurement choices
{: #measurement}

Two further choices make the phenomenon harder to see, independently of the argument above. We revisit both in Section 4.

- First, performance is measured by ROC AUC, which is rank-based and saturates: once an early layer separates the classes, later layers can sharpen or suppress the true class without moving the metric, so a flat ROC AUC curve is uninformative about what the late layers do. 
- Second, layers are removed by skipping, which is a form of zero ablation. Zero ablation sets a component's contribution to a value the network never encounters during training, which takes the residual stream off-distribution and does not preserve its norm; the resulting perturbation is large but not representative.

### 3.4 What we do not dispute
{: #not-disputed}

The conclusion Balef et al. draw alongside the repair claim — that the layers overlap in functionality — holds in both worlds, and their evidence supports it. Their headline result, substantial depthwise redundancy and a looped single-layer model that reaches comparable performance with 20% of the parameters, rests on that overlap and is untouched by the present analysis. Indeed, our finding points in the same direction as theirs. What we revise is the mechanism: Balef et al. explicitly pose the right dichotomy — redundancy or repair — but adopt a criterion that both alternatives satisfy, and we resolve it with a quantity their design does not contain.

## 4. Measuring the direct effect in TFMs
{: #method}

Our measurement has three ingredients. The first supplies the missing counterfactual; the other two make the resulting quantities readable.

**Ingredient 1: the direct effect via path patching.** The main idea is as follows. To learn what layer \\(m\\) contributes on its own, we run the model once on the clean table, record every layer's write, and then construct the output the model *would* have produced if layer \\(m\\) had written something else and no other layer had noticed. Formally, let \\(r_\ell\\) denote the residual stream after layer \\(\ell\\) and \\(a_m = r_m - r_{m-1}\\) the write of layer \\(m\\). Substituting a donor write \\(\tilde{a}_m\\) while reusing every downstream write \\(a_\ell\\), \\(\ell > m\\), at its clean value — that is, not recomputing it on the perturbed residual — gives

$$r_L^{DE}(m) = r_L^{\text{clean}} - a_m + \tilde{a}_m ,$$

which we then push through the model's **native** decoder, recomputing the final LayerNorm statistics on the patched residual. The total effect is obtained from the same substitution with the downstream layers free to react, i.e., by re-running the forward pass, and read through the same native decoder. Figure 4 contrasts the two.

![Clean, TE and DE](/assets/img/tfm-self-repair/path-patching.png)
*Figure 4: the clean pass, the total effect, and the direct effect. The direct effect requires no second forward pass, since reusing the recorded downstream writes reduces to arithmetic on the clean residual.*

Two properties matter. First, \\(DE\\) and \\(TE\\) are read on the same ruler, the model's own decoder, which is what makes their difference interpretable. Second, we deliberately do not use a logit lens as a stand-in for \\(DE\\): reading a fixed direction \\(\hat{u}^\top a_m\\) out of the write gives a belief trajectory rather than a contribution meter, and it is not faithful under the final LayerNorm, whose scale depends on the norm of the residual it receives.

**Ingredient 2: a logit margin instead of ROC AUC.** For a binary task with true-class logit \\(z_{i,y_i}\\) and competitor logit \\(z_{i,1-y_i}\\) on query row \\(i\\), we define the margin \\(\gamma_i = z_{i,y_i} - z_{i,1-y_i}\\) and take its median over rows. We use the median because ablation pushes some rows off-distribution and the resulting LayerNorm extrapolation produces a heavy tail that hijacks a mean. Normalizing by the clean final-layer margin of the same task makes \\(0\\) the decision boundary, \\(1\\) the model's clean final confidence, and negative values a flipped decision. Unlike ROC AUC, the margin is a magnitude and does not saturate.

**Ingredient 3: resample ablation instead of zero ablation.** We take the donor write \\(\tilde{a}_m\\) from a forward pass on a *different* table, matched by role, i.e., the same layer and the same position along both attention axes, across rows and across features, and we average the result over 8 donors. The reason is that resampling keeps the substituted activation on the manifold the model was trained on and approximately preserves its norm, so the intervention asks what the layer would have contributed on other data rather than what happens when it ceases to exist. This addresses the second concern of Section 3. We report the quantitative comparison against zero ablation in Appendix A.

**Criterion.** Given \\(DE\\) and \\(TE\\) we compute \\(CE = DE - TE\\) and apply the criterion of Section 2. We say that a model exhibits self-repair if (i) the \\((DE, TE)\\) cloud has mass below the diagonal with \\(CE > 0\\) of a magnitude comparable to \\(DE\\), and (ii) \\(CE\\) grows with \\(DE\\) in a tight per-layer regression, as in language models. We require both because the first alone is a statement about location and the second is what distinguishes a mechanism from noise.

## 5. Experiments
{: #experiments}

**Models.** We evaluate four TFMs: [LimiX-2M](https://huggingface.co/stableai-org/LimiX-2M), [Mitra](https://huggingface.co/autogluon/mitra-classifier), [TabICL-v2](https://huggingface.co/jingang/TabICL), and [TabFM](https://huggingface.co/google/tabfm-1.0.0-pytorch). Mitra plays a special role, because its output head is linear, so the decomposition \\(TE = DE + IE\\) holds exactly and any observed \\(CE\\) cannot be attributed to a nonlinearity in the readout.

**Datasets.** We use 15 binary classification tasks from TabArena, with 1000 support rows and 500 query rows per task.

**Ablation unit and parameters.** We ablate one transformer layer at a time, over all layers of each model, using resample ablation averaged over 8 donor tables. Each point in the figures below is one (layer, task) pair.

**Metrics.** We report the margin of Section 4 as the primary coordinate and the mean true-class logit, z-scored per task, as a secondary coordinate for robustness.

### 5.1 What a better metric buys, and what it does not
{: #metrics}

We first isolate the effect of replacing ROC AUC by the margin, using the layer-skipping protocol of Section 3. Three regimes appear, which ROC AUC collapses into a single flat line.

![Net-suppressive layer](/assets/img/tfm-self-repair/auc-takeaway1.png)
*Figure 5: Mitra, skipping the final layer. ROC AUC does not move; the margin overshoots to 1.53.*

**Net-suppressive layers.** Skipping the last layer of Mitra leaves ROC AUC at its ceiling, which reads as "this layer does nothing", while the margin overshoots to 1.53, i.e., deleting the layer makes the model *more* confident in the true class. The layer is net-suppressive and ROC AUC is blind to it.

![Dip and recovery under the margin](/assets/img/tfm-self-repair/auc-takeaway2.png)
*Figure 6: Mitra, skipping layer 6. ROC AUC rides the ceiling; the margin collapses to 0.36 and climbs back to 0.87.*

**Dip and recovery.** Skipping layer 6 of Mitra also leaves ROC AUC flat, while the margin collapses from 0.85 to 0.36 and climbs back to 0.87 against a baseline of 0.97 — the phenomenon of Section 3, invisible under ROC AUC. Note that the two regimes have opposite signs and yet produce the same ROC AUC curve, which is the sense in which the metric hides signed work.

![All late layers fold back onto the baseline](/assets/img/tfm-self-repair/auc-tabfm_triple.png)
*Figure 7: TabFM, all three lenses. After the immediate dip, every late-layer skip folds back onto the baseline in ROC AUC, margin and true-class logit alike.*

**The residual ambiguity.** However, a better metric does not resolve the question. In the late stack of TabFM every skipped layer returns to the baseline at the output, so \\(TE \approx 0\\) under the margin exactly as under ROC AUC, model-wide rather than for a hand-picked layer. Thus the sharper metric relocates the problem without solving it, which is what Section 3 predicts: the obstruction is the design, not the resolution of the measurement.

### 5.2 The compensation effect is approximately zero
{: #main-result}

Figure 8 is our main result: the direct effect against the total effect for all four models, one point per (layer, task) pair.

![DE versus TE for four TFMs](/assets/img/tfm-self-repair/de-te-scatter.png)
*Figure 8: direct effect against total effect, margin coordinate, four TFMs. The dashed line is \\(y = x\\), i.e., no downstream reaction. The below-diagonal cloud of Figure 2 is absent.*

We read the figure against the key of Section 2, with one region added: points with \\(DE \approx 0\\) but large \\(\lvert TE \rvert\\) are *indirectly important*, i.e., they build features that later layers consume. We observe three things. First, the late layers of every model sit on the diagonal with \\(DE > 0\\): they write the decision directly and nothing compensates for them. Second, the remaining layers form a vertical band at \\(DE \approx 0\\), which means most layers do not write the decision directly. Third, and centrally, the below-diagonal repair cloud of Figure 2 does not appear. The mean compensation effect is \\(+0.09\\) for LimiX-2M, \\(+0.01\\) for Mitra, \\(-0.08\\) for TabICL and \\(+0.00\\) for TabFM, in units where the clean final margin is \\(1\\).

We report the fraction of points in each region, using a threshold of \\(0.1\\) on the margin.

| model | redundant | indirectly important | load-bearing | repaired | amplified |
|---|---|---|---|---|---|
| LimiX-2M | 16% | 53% | 15% | 11% | 3% |
| Mitra | 44% | 40% | 10% | 2% | 2% |
| TabICL-v2 | 41% | 40% | 13% | 0% | 5% |
| TabFM | 78% | 10% | 7% | 1% | 1% |

*Table 2: share of (layer, task) pairs in each region of Figure 8, margin coordinate, threshold \\(0.1\\). The repaired region holds 0–11% in every model.*

Two observations follow. First, the repaired region holds 0–11% of the points in every model, which is the quantitative form of the null. Second, the layers with \\(DE \approx 0\\) split in a model-dependent way: TabFM is dominated by genuine redundancy (78% at the origin), whereas in LimiX-2M most such layers are indirectly important (53%), i.e., replacing their write with a donor's does damage the output even though they do not write the decision themselves. Note that "indirectly important" is a substantive finding and not an artifact of noise, because the substitution is a role-matched donor write: if an arbitrary donor still damages the output, the specific computation of that layer is not interchangeable.

In the interest of reporting the strongest evidence against our conclusion, we note that 65% of LimiX-2M points and 64% of Mitra points lie below the diagonal. Taken alone this would suggest a weak repair tendency. However, the location of the median is not the criterion of Section 4: the associated magnitudes are \\(+0.09\\) and \\(+0.01\\) against a clean final margin of \\(1\\), and the second requirement — a proportional compensation law — fails, as we show next.

### 5.3 Robustness
{: #robustness}

We check the null at a finer resolution, against the compensation law, and against a possible artifact of the readout.

**Per-row de-aggregation.** Each point in Figure 8 aggregates roughly 500 query rows, so a null mean is compatible with (i) cancellation between rows of opposite sign or (ii) strong repair confined to a small subpopulation. We therefore de-aggregate and inspect the distribution of \\(CE\\) over individual rows.

![Per-row compensation effect](/assets/img/tfm-self-repair/per-row-ce-hist.png)
*Figure 9: distribution of the per-row compensation effect. All four models are unimodal at zero.*

The per-row distributions are unimodal at zero in all four models, neither bimodal nor right-skewed, which rules out both alternatives. Restricting to rows with \\(\lvert DE \rvert > 0.1\sigma\\), the below-diagonal share ranges from 23% to 58% across models, i.e., close to a coin flip, and the only signal that is stable across tables is negative \\(CE\\) in TabICL — amplification, which is the opposite of repair.

**The compensation law.** We repeat the analysis of McGrath et al. by regressing \\(CE\\) on \\(DE\\) across the 15 tasks, separately for each layer, and reporting the layer at which \\(R^2\\) peaks.

| model | apex layer | slope | \\(R^2\\) |
|---|---|---|---|
| LimiX-2M | L10 | \\(-0.03\\) | 0.00 |
| Mitra | L10 | \\(+0.08\\) | 0.04 |
| TabICL-v2 | L10 | \\(-0.41\\) | **0.58** |
| TabFM | L18 | \\(-0.08\\) | 0.04 |
| *Chinchilla 7B [[1](#ref-1)]* | *L23* | *\\(+0.69\\)* | *0.92* |

*Table 3: per-layer regression of \\(CE\\) on \\(DE\\) across the 15 tasks, reported at the layer where \\(R^2\\) peaks. The last row is the language-model reference. No TFM layer combines a high \\(R^2\\) with a slope in \\((0, 1)\\).*

![Per-layer CE against DE](/assets/img/tfm-self-repair/compensation-fit.png)
*Figure 10: per-layer regression of the compensation effect on the direct effect. No layer reproduces the language-model relation.*

No layer in any model combines a high \\(R^2\\) with a slope in \\((0, 1)\\), which is the joint signature of partial, systematic compensation. The one layer with genuine structure, TabICL L10 with \\(R^2 = 0.58\\), has a *negative* slope, i.e., the larger the direct contribution, the more the downstream layers amplify its loss. This is amplification, not repair.

**The test is biased in favour of repair.** Moreover, the regression is generous to the hypothesis we are rejecting. Since \\(CE = DE - TE\\), the regressand contains a \\(+DE\\) term, so any noise in \\(DE\\) propagates into a spurious positive association between \\(CE\\) and \\(DE\\). The test is therefore biased towards finding a compensation law, and it fails even so, which makes the null more robust rather than less.

**Not an artifact of the readout.** Finally, Mitra serves as an anchor. Its head is linear, so \\(TE = DE + IE\\) holds with zero residual and the decomposition is exact. Mitra shows \\(CE = +0.01\\), i.e., the null is not produced by nonlinearity in the decoder or by LayerNorm rescaling. In addition, the null holds under the secondary coordinate: the mean \\(CE\\) in the true-class logit is \\(-0.00\\), \\(-0.04\\), \\(-0.43\\) and \\(+0.04\\) for the four models, and the one large value, TabICL, is negative and shrinks to \\(-0.08\\) under the robust margin, which identifies it as an early-layer tail effect rather than compensation.

## 6. Why redundancy is cheap in tabular models
{: #discussion}

Our results say what the recovery is not; we close by describing what we believe it is, with the caveat that this section is a hypothesis rather than a result. The intuition is that active repair and passive redundancy are not equally expensive in every architecture. In a language model, the prompt is consumed once and the information a layer needs may exist nowhere else in the residual stream, so recovering it requires a dedicated mechanism, e.g., a backup head that activates only when the primary head is removed. In a TFM, the input table is in context for the entire forward pass, so any layer can recompute a lost feature from the raw values at the cost of ordinary attention. Redundancy is therefore the path of least resistance, and no reactive mechanism needs to be learned.

This account is coherent with the picture of Balef et al. [[5](#ref-5)], in which depth performs iterative refinement with overlapping computations rather than a sequence of specialized stages, and it explains why "a large dip followed by full recovery" is the typical trajectory: the probe at depth \\(m+1\\) is sensitive to the missing write, while the information itself was never scarce. Testing it requires interventions we have not run, and we leave this to future work.

## 7. Limitations
{: #limitations}

Three limitations bound the claim. **Granularity.** We ablate whole layers, whereas the compensation reported in language models is head-level. A layer averages several heads, so repair confined to individual heads could in principle cancel within a layer. Per-head direct effects are the one finer resolution we have not measured. **Scope.** We study four models, 15 binary classification tasks, one checkpoint per model, and donor tables drawn from the same benchmark; multiclass and regression tasks are not covered. Two of our models, LimiX-2M and TabICL, are also studied by Balef et al. [5], but the TabPFN family is not, and it is for TabPFN(v2) that they report the clearest self-repair. For that model our objection is therefore the identifiability argument of Section 3 alone, i.e., that the criterion does not establish the conclusion, and not the direct evidence of Section 5. **Ablation operator.** Resample ablation removes a layer's *specific* computation but not its average contribution, which is the correct operator for our question but not the only defensible one.

Against these, the three independent checks of Section 5.3 agree, and the compensation test is tilted towards the hypothesis it rejects.

## 8. Conclusion
{: #conclusion}

We measure self-repair in TFMs as the gap between the direct and the total effect of a component. Across four models and 15 tasks the compensation effect is approximately zero, at layer and at row resolution, and the proportional compensation law observed in language models does not hold at any layer. The drop-and-recovery phenomenon reported for TFMs is therefore passive redundancy rather than active compensation. To our knowledge, this is the first measurement of the direct effect in tabular foundation models, and it is the quantity that makes the redundancy-or-repair question decidable.

Our work opens interesting directions for future research. For example, (i) does per-head resolution reveal compensation that layer-level ablation averages away? (ii) where does the duplicated information live — is it copied along the residual stream, or recomputed from the input table on demand? (iii) how does the redundancy degrade under joint ablation of \\(k\\) layers, and is the degradation threshold-like or linear in \\(k\\)? and (iv) can the direct effect serve as a routine diagnostic for attribution in TFMs, given that ablation alone systematically misreads component importance?

## 9. Reproducibility
{: #reproducibility}

All code, the ablation sweeps, and the scripts that regenerate every figure in this paper are available in the [`tfmlens`](https://github.com/xiaohan2012/tfmlens) repository. Model checkpoints are the public releases linked in Section 5; tasks are the binary classification subset of [TabArena](https://tabarena.ai).

---

## References

<span id="ref-1"></span>[1] T. McGrath, M. Rahtz, J. Kramar, V. Mikulik, S. Legg. *The Hydra Effect: Emergent Self-repair in Language Model Computations.* [arXiv:2307.15771](https://arxiv.org/abs/2307.15771), 2023.

<span id="ref-2"></span>[2] K. Wang, A. Variengien, A. Conmy, B. Shlegeris, J. Steinhardt. *Interpretability in the Wild: a Circuit for Indirect Object Identification in GPT-2 small.* [arXiv:2211.00593](https://arxiv.org/abs/2211.00593), 2022.

<span id="ref-3"></span>[3] C. Rushing, N. Nanda. *Explorations of Self-Repair in Language Models.* [arXiv:2402.15390](https://arxiv.org/abs/2402.15390), 2024.

<span id="ref-4"></span>[4] J. Miller, B. Chughtai, L. Sharkey. *Transformer Circuit Faithfulness Metrics are not Robust.* [arXiv:2407.08734](https://arxiv.org/abs/2407.08734), 2024.

<span id="ref-5"></span>[5] A. R. Balef, M. Koshil, K. Eggensperger. *Is One Layer Enough? Understanding Inference Dynamics in Tabular Foundation Models.* [arXiv:2605.06510](https://arxiv.org/abs/2605.06510), 2026.

<span id="ref-6"></span>[6] J. Pearl. *Direct and Indirect Effects.* Proceedings of the 17th Conference on Uncertainty in Artificial Intelligence (UAI), 2001.

---

## Appendix A: resample versus zero ablation
{: #appendix-a}

*To be filled from the existing comparison: zero ablation produces larger swings and overshoots, i.e., the immediate margin change becomes negative, meaning the ablation improves the margin — a signature of an off-distribution perturbation rather than of removed computation.*

## Appendix B: the layer-skipping experiment on all four models
{: #appendix-b}

![Dip and recovery across four TFMs](/assets/img/tfm-self-repair/balef-exp6-repro.png)
*Figure 11: our reproduction of the layer-skipping experiment of Balef et al. [[5](#ref-5)] on four TFMs, 15 tasks each. Black is the unablated baseline; each coloured curve skips one layer, marked by a red cross. The right panel of Figure 3 is the LimiX-2M panel of this figure.*
