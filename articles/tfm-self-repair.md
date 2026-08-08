---
layout: post
title: "Do tabular foundation models repair themselves?"
desc: "Ablate a layer in a tabular foundation model and the answer comes back. Measuring the direct effect says nobody repaired anything."
mathjax: true
permalink: /articles/tfm-self-repair/
date: 2026-08-08
---

Delete an important attention head from a language model and the output barely moves. Downstream components notice the gap and step in. McGrath et al. called this the **[Hydra Effect](https://arxiv.org/abs/2307.15771)** — cut off one head, another grows back.

Tabular foundation models (TFMs) are transformers too. They do in-context learning over a table instead of text, but the architecture is the same shape. **Do they self-repair?**

This post is mostly about *how to ask that question well*. The answer turns out to be no, and the reason a naive experiment says yes is instructive.

Code: [xiaohan2012/tfmlens](https://github.com/xiaohan2012/tfmlens).

## Table of contents
{:.no_toc}

* TOC
{:toc}

## 1. What counts as self-repair
{: #definition}

Self-repair is not a phenomenon you can observe directly. It is a **difference between two counterfactuals**.

The definition (McGrath et al. 2023): ablate a component, downstream **actively compensates**, so the net damage is far smaller than the component's own importance. To make "compensates" measurable, run two different interventions on layer \\(m\\):

- **DE — direct effect.** Remove \\(m\\)'s write, but **freeze everything downstream at its clean value**. This is what \\(m\\) contributes *directly*: the damage if nobody reacts.
- **TE — total effect.** Remove \\(m\\)'s write and let the forward pass run normally. Downstream reacts. This is the *net* damage.
- **CE — compensation effect.** \\(\mathrm{CE} = \mathrm{DE} - \mathrm{TE}\\): how much downstream made up for.

$$\textbf{self-repair} \iff \mathrm{CE} > 0 \iff \mathrm{TE} \ll \mathrm{DE}$$

This is standard causal mediation, and the Hydra paper draws it exactly this way:

![Total, direct and indirect effects](/assets/img/tfm-self-repair/hydra-te-de-ie.png)

*The three counterfactuals, from McGrath et al. 2023. The spine is the residual stream, \\(\oplus\\) is a residual add, \\(a\\) is the ablated layer and \\(b\\) a downstream one. **Total effect**: intervene on \\(A\\), let \\(B\\) react. **Direct effect**: intervene on \\(A\\) while pinning \\(B\\) to its clean value with \\(do(B=b)\\). **Indirect effect**: the part that flows through \\(B\\).*

Because \\(\mathrm{TE} = \mathrm{DE} + \mathrm{IE}\\), our compensation effect is just the negated indirect effect:

$$\mathrm{CE} = \mathrm{DE} - \mathrm{TE} = -\,\mathrm{IE}$$

Self-repair, in one line: **the mediator's path opposes the direct damage**.

### Why one number is never enough

This is the part that decides everything downstream, so it is worth being slow about.

A three-node toy is enough. Residual stream, scalar, linear readout:

$$y = x + a + b, \qquad x = 0, \qquad y_{\text{clean}} = 1$$

where \\(a\\) is the layer we ablate (set its write to \\(0\\)) and \\(b\\) is a downstream layer. Three different models, all with the same clean output:

| | \\(a\\) writes | \\(b\\)'s rule | clean \\(y\\) | **DE** | **TE** | **CE** |
|:--|:--:|:--:|:--:|:--:|:--:|:--:|
| ① redundant — \\(a\\) writes no decision | 0 | \\(b = 1\\) always | 1 | **0** | **0** | 0 |
| ② repaired — \\(b\\) is a backup | 1 | \\(b = 1 - a\\) | 1 | **1** | **0** | **1** |
| ③ load-bearing | 1 | \\(b = 0\\) always | 1 | **1** | **1** | 0 |

Row ② is the only interesting one, and it takes ten seconds to check by hand:

- **DE** — pin \\(b\\) at its clean value \\(0\\): \\(y = 0 + 0 + 0 = 0\\), so \\(\mathrm{DE} = 1 - 0 = 1\\).
- **TE** — let \\(b\\) react: \\(b = 1 - 0 = 1\\), so \\(y = 0 + 0 + 1 = 1\\) and \\(\mathrm{TE} = 1 - 1 = 0\\).
- **CE** \\(= 1 - 0 = 1\\). Layer \\(a\\) mattered, and \\(b\\) covered for it entirely.

Now read the three columns:

- **TE** gives \\(0, 0, 1\\) → it **cannot separate ① from ②**. A layer that never mattered and a layer that mattered and was fully repaired produce the identical reading.
- **DE** gives \\(0, 1, 1\\) → it **cannot separate ② from ③**. Load-bearing and repaired both look directly important.
- **CE** gives \\(0, 1, 0\\) → **only the gap isolates ②**.

That is not a quirk of the toy. "Downstream compensated" *is* the statement "direct importance exceeds net importance" — a comparison of two quantities. No single measurement can express it.

One honest caveat about row ①:

- **Duplicated information is not row ①.** Under a linear readout, \\(a=1\\) *and* \\(b=1\\) lands on row ③ — removing one copy really does move the logit.
- **Row ① is the stricter claim:** this layer writes no decision at all.
- Real models contain both. [§4](#results) splits them apart.

## 2. Measuring the direct effect
{: #measuring-de}

TE is easy: ablate and re-run. **DE is the hard half**, and two things make it hard.

### Freezing downstream without a per-layer forward

Naively, DE needs a surgical forward pass per layer: ablate \\(m\\), then pin every downstream layer's output to what it was in the clean run. That is expensive and fiddly.

The residual stream makes it free. Writes are **additive**, so the final residual is a sum, and freezing downstream just means keeping the clean summands:

$$r_L^{\mathrm{DE}}(m) = r_L^{\text{clean}} - a_m + \tilde{a}_m, \qquad a_m = r_m - r_{m-1}$$

One clean forward captures every \\(a_m\\) at once. After that, DE for **all** layers is arithmetic on a tensor we already have.

![Clean, TE and DE](/assets/img/tfm-self-repair/path-patching.png)

*Same three graphs as the definition figure, drawn for a transformer. **Clean** (left): one forward gives every \\(a_m\\). **TE** (middle): inject \\(\tilde{a}_m\\) and re-run — downstream recomputes, everything above \\(m\\) changes. **DE** (right): the residual above \\(m\\) changes, but the downstream **writes** are reused from the clean pass (dashed) rather than recomputed. No second forward.*

<details markdown="1">
<summary><strong>Why this is path patching and not a logit lens</strong> (the distinction that makes DE trustworthy)</summary>

There are two ways to turn "layer \\(m\\)'s contribution" into a number, and they are not the same measurement.

**Method A — the logit-lens style.** Take the layer's write \\(a_m\\), project it onto a fixed readout direction \\(\hat{u}\\), and call \\(\hat{u}^\top a_m\\) the direct effect. Cheap, and it is what most "which layer writes the answer" plots do. But it assumes the readout is a fixed linear functional of that one write, which is false as soon as there is a final LayerNorm: the normalisation constant depends on the *whole* residual, so removing \\(a_m\\) changes how every other write is scaled.

**Method B — what we do.** Build the full patched residual \\(r_L^{\mathrm{DE}}(m)\\) and push it through the model's **actual** readout: the stream/token selection, the final LayerNorm with \\(\sigma\\) **recomputed on this residual**, then the native decoder. The decoder sees a complete residual, exactly as it would in a real forward pass.

The difference matters most in early layers, where method A's raw-logit numbers blow up and method B's stay bounded.

One consequence worth flagging:

- \\(\mathrm{CE} = \mathrm{DE} - \mathrm{TE}\\) shares a \\(+\mathrm{DE}\\) term with the regressor.
- So regressing CE on DE is **mechanically biased toward a positive relationship**.
- Any compensation law is therefore graded on a generous curve — **failing anyway is the strong result**. Back to this in [§5](#robustness).
</details>

### One ruler for both effects

DE and TE have to be comparable, which means reading them through **the same** decoder. We use the model's **own** final decoder for both.

The alternative — a **per-depth fine-tuned probe**, one decoder trained per depth — measures something real, but something else:

- **Decodability, not use.** How well the answer can be *extracted* from a residual ≠ how much the model's actual readout *uses* it.
- **No shared scale.** Each depth has its own probe, so two depths are not comparable.

For DE vs TE that is fatal, because the whole quantity is a difference between two readings. They must be on one ruler.

### What to replace the write with

Ablation needs a replacement value \\(\tilde{a}_m\\). Zeroing the layer is the obvious choice and the wrong one: a zero write is far off the distribution the rest of the network expects, so the damage you measure is partly shock, not information loss.

We use **resample ablation** instead:

- \\(\tilde{a}_m\\) = a role-matched write from a *different table's* forward pass — on-manifold, norm-preserving, averaged over 8 donors.
- Side by side, **zero swings much harder** per layer, and sometimes **overshoots**: ablating a layer *improves* the margin, a tell-tale off-manifold artefact.
- Resample does not.

### Coordinates

Everything is read on two coordinates, both z-scored per task so tasks and models can be pooled:

- **GT-logit** — the logit of the true class.
- **Margin** — true-class logit minus the other class, reduced by median. Robust; closest to "did the decision actually move".

Two coordinates means every claim below gets a free robustness check.

## 3. What the definition looks like on a plot
{: #criterion}

Before any data: the definition translates directly into a region of a scatter plot. Put \\(\mathrm{DE}\\) on \\(x\\), \\(\mathrm{TE}\\) on \\(y\\), one point per (layer, task). The diagonal \\(y = x\\) is "no compensation".

| region | DE | TE | what the layer is |
|:--|:--:|:--:|:--|
| on the diagonal | \\(>0\\) | \\(=\mathrm{DE}\\) | **load-bearing** — writes the decision, nobody covers |
| **below the diagonal** | \\(>0\\) | \\(\ll \mathrm{DE}\\) | **self-repair** — the only signature there is |
| at the origin | \\(\approx 0\\) | \\(\approx 0\\) | **redundant** — writes no decision |
| left edge | \\(\approx 0\\) | large | **indirectly important** — builds features others need |
| above the diagonal | \\(>0\\) | \\(>\mathrm{DE}\\) | breakage / amplification |

Declaring the criterion before looking at the data is the point. Here is what a **positive** result looks like — the same plot from the Hydra paper:

![Hydra Effect reference scatter](/assets/img/tfm-self-repair/hydra-ref-scatter.png)

*McGrath et al. 2023. The mass sits clearly **below** the diagonal: layers with large direct effect whose total effect is much smaller. That is self-repair.*

## 4. Results
{: #results}

**Setup.** Four tabular foundation models, at the exact checkpoints linked:

- [**LimiX-2M**](https://huggingface.co/stableai-org/LimiX-2M) · [**Mitra**](https://huggingface.co/autogluon/mitra-classifier) · [**TabICL-v2**](https://huggingface.co/jingang/TabICL) · [**TabFM**](https://huggingface.co/google/tabfm-1.0.0-pytorch)
- **15 TabArena** binary-classification tasks; 1000 training rows, 500 test rows each.
- Resample ablation, 8 donors, every layer ablated in turn.

![DE vs TE across four TFMs](/assets/img/tfm-self-repair/de-te-scatter.png)

*DE vs TE, one point per (layer, task), z-scored per task, margin coordinate. Compare with the Hydra plot above: the below-diagonal cloud is missing.*

Mean CE over the non-redundant points:

| model | mean CE (margin, \\(\sigma\\)) | mean CE (GT-logit, \\(\sigma\\)) |
|:--|:--:|:--:|
| LimiX-2M | +0.09 | −0.00 |
| Mitra | +0.01 | −0.04 |
| TabICL-v2 | −0.08 | −0.43 |
| TabFM | +0.00 | +0.04 |

**CE \\(\approx 0\\) everywhere.** The sign of CE is roughly a coin flip across points, and the magnitude is a rounding error next to Hydra's. TabICL's apparent −0.43 on GT-logit collapses to −0.08 on the margin, which marks it as an early-layer raw-logit tail rather than a real effect — exactly the kind of thing the second coordinate is there to catch.

### A positive finding on the way past

"DE \\(\approx 0\\) dominates" is true but too coarse. Splitting it by TE reveals that the four architectures divide labour very differently:

| model | redundant<br>(DE≈0, TE≈0) | indirectly important<br>(DE≈0, TE large) | load-bearing | repair | amplification |
|:--|:--:|:--:|:--:|:--:|:--:|
| **TabFM** | **78%** | 10% | 7% | 1% | 1% |
| **Mitra** | 44% | 40% | 10% | 2% | 2% |
| **TabICL-v2** | 41% | 40% | 13% | 0% | 5% |
| **LimiX-2M** | **16%** | **53%** | 15% | 11% | 3% |

- Most layers **do not write the decision** (69–88%), in every model.
- But the split inside that is architecture-specific: **TabFM is mostly plain redundancy**, while **LimiX-2M is mostly indirect importance** — its early and middle layers do feature work that later layers depend on, and removing them really does hurt.
- **Repair is 0–11% everywhere**, and the 11% for LimiX comes with a mean CE of +0.09σ — small points, not a cloud.

The "indirectly important" column is not an artefact of a broken ablation, incidentally: we replace a layer's write with a *real write from another table*. If an arbitrary but plausible substitute still hurts, that layer's specific computation is load-bearing for something downstream.

## 5. Three ways to break the null
{: #robustness}

A flat mean is the weakest possible evidence. Three specific ways CE ≈ 0 could be a mirage, and what happens when you check each.

### Could row aggregation be cancelling signs?

Each table's 500 test rows are collapsed into one number — so a real mechanism could average itself away.

- **The worry:** half the rows repair, half break, the mean reads zero.
- **The test:** un-aggregate to one point per (task, layer, **test row**), and read the *shape* of the CE distribution.

The three hypotheses make different predictions:

| shape of per-row CE | interpretation |
|:--|:--|
| unimodal at 0 | genuinely flat — no repair to hide |
| bimodal | sign cancellation — repair on some rows, breakage on others |
| right-skewed | a diluted subpopulation that does repair |

![Per-row CE histograms](/assets/img/tfm-self-repair/per-row-ce-hist.png)

*Per-row CE, pooled over layers and tasks, non-redundant rows only. Unimodal at zero in all four models. Medians: +0.00, −0.00, −0.07, +0.03.*

**Unimodal at zero.** The aggregate null is real, not an average of two opposite stories.

### Could the repair be concentrated in a few layers?

Hydra's actual claim is not about one hand-picked layer — it is a **compensation law** across depth:

- Regress CE on DE **separately at each layer**.
- Both \\(R^2\\) and slope form a **band** across the middle-to-late layers.
- Apex at layer 23: \\(R^2 = 0.92\\), slope \\(= 0.69\\).

![Hydra per-layer regression](/assets/img/tfm-self-repair/hydra-ref-regression.png)

*McGrath et al. 2023: per-layer \\(R^2\\) and slope against depth. The structure is the claim.*

Same analysis on the TFMs:

![Per-layer compensation fit](/assets/img/tfm-self-repair/compensation-fit.png)

*Per-layer CE~DE regression, \\(R^2\\) (top) and slope (bottom) against depth. Green band = the self-repair region, slope in \\((0,1)\\). Hollow markers = layers with no DE spread, where the slope is meaningless.*

| model | apex layer | \\(R^2\\) | slope |
|:--|:--:|:--:|:--:|
| LimiX-2M | L10 | 0.00 | −0.03 |
| Mitra | L10 | 0.04 | +0.08 |
| TabICL-v2 | L10 | **0.58** | **−0.41** |
| TabFM | L18 | 0.04 | −0.08 |
| *Hydra (reference)* | *L23* | *0.92* | *+0.69* |

No band, no law. The one layer with real structure — TabICL's L10, \\(R^2 = 0.58\\) — has a **negative** slope, which is amplification, the opposite of repair.

And recall the bias noted in [§2](#measuring-de): \\(\mathrm{CE} = \mathrm{DE} - \mathrm{TE}\\) shares a \\(+\mathrm{DE}\\) term with the regressor, so this test is tilted *in favour of* finding a positive law. It still finds nothing.

### Could LayerNorm be faking the flatness?

The decomposition \\(\mathrm{TE} = \mathrm{DE} + \mathrm{IE}\\) is only exact for a linear readout. With a LayerNorm in the way there is a nonlinearity residual, and one could argue that a real CE is being absorbed into it.

**Mitra is the control.** Its readout is LayerNorm followed by a linear head, so folding the normalisation constant makes the decomposition exact — no residual term to hide anything in. Mitra's CE is +0.01σ. The null survives its cleanest test.

## 6. The observation that looks like self-repair
{: #dip-recover}

There is a separate experiment on TFMs that appears to settle the question on its own, and it is worth explaining why it does not.

[Balef et al., *Is One Layer Enough?*](https://arxiv.org/abs/2605.06510) ablate a layer and then decode **every depth** with a per-depth fine-tuned probe. The trajectory is striking: the answer collapses right after the ablated layer, then climbs back to baseline by the final layer. Call it **dip-and-recover**.

![Dip and recover across four TFMs](/assets/img/tfm-self-repair/dip-recover.png)

*Ablate layer \\(m\\) (one coloured line per \\(m\\)), decode at every depth with a per-depth fine-tuned decoder. Black is the un-ablated baseline. The immediate drop is marked with a red ×; by the final layer it is gone.*

The phenomenon is real — we reproduce it in all four models above. But the same shape comes out of **two different mechanisms**:

- **Active repair.** Downstream notices the missing write and produces a replacement. Somebody rewrites something.
- **Passive redundancy.** The information was **already duplicated** elsewhere in the residual. Deleting one copy upsets a probe reading *at that depth*, but the other copies survive, so the final readout decodes the answer anyway. **Nobody rewrote anything.**

Dip-and-recover only demonstrates that the final residual is still decodable. Redundancy guarantees that for free.

A large dip with \\(\mathrm{TE} \approx 0\\) is not a contradiction — it is **precisely the redundancy signature**:

- the probe at depth \\(m{+}1\\) leans heavily on that layer's write → **big dip**;
- the information itself lives in several places → **nothing left to lose by the end**.

**TFMs are structurally biased toward the passive version.** The input table stays in context for the entire forward pass. Any later layer that needs a feature can simply recompute it. A language model has to maintain a backup head that fires when the primary one goes missing; a TFM does not need one, because the raw evidence never left the room.

Which is, in the end, the more interesting version of the result: TFMs do not self-repair *because they do not have to*.

## 7. Relation to "Is One Layer Enough?"
{: #relation}

Their Exp6 is the dip-and-recover experiment from [§6](#dip-recover). Read against the definition in [§1](#definition):

- The trajectory's "recovery to baseline" **is** \\(\mathrm{TE} \approx 0\\) — net damage at the final layer, with downstream free to react.
- That is the **ambiguous** cell. Redundant and repaired give the same reading.
- Deciding between them needs DE, which needs a frozen-downstream intervention that the trajectory experiment does not perform.

So the missing piece is not a stronger version of that measurement — it is **the other half of the pair**. That is what this post adds.

## 8. What's next
{: #next}

If the recovery is passive redundancy, the obvious follow-up is: **what kind?**

- **Copies exist** — probe every depth; is the answer near-perfectly decodable long before the load-bearing layers?
- **Recompute from the table** — co-ablate the layer *and* the attention path to the input rows. If recovery vanishes, later layers were re-deriving rather than reading a cached copy.
- **How many layers does it take?** Ablate \\(k\\) layers at once. Genuine redundancy should show a threshold in \\(k\\); indirect importance should degrade linearly from \\(k = 1\\). Cheapest and most decisive of the three.
- **Who writes the copies** — attention or MLP.

Tracking that work in [issue #47](https://github.com/xiaohan2012/tfmlens/issues/47).

---

### The transferable part

Strip out the tabular specifics and one methodological point remains:

> **\\(\mathrm{TE} \approx 0\\) is not evidence of repair.** It is evidence of *either* repair *or* irrelevance, and those are opposite mechanisms. Reporting compensation requires the frozen-downstream counterfactual too, read on the same ruler as the total effect.

Ablation studies are cheap, and TE is the one they all measure.

---

*Figures marked "McGrath et al. 2023" are from [The Hydra Effect: Emergent Self-repair in Language Model Computations](https://arxiv.org/abs/2307.15771), reproduced here for comparison. All other figures, data and code: [xiaohan2012/tfmlens](https://github.com/xiaohan2012/tfmlens).*
