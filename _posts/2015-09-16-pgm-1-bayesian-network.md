---
layout: post
title: "PGM course: Bayesian Network"
date: 2015-09-16 15:58:13
categories: course
tags: pgm bayesian-network
---

# Learned from book

## Exploiting Independence Properties

- Why represent the joint probability in a compact way: computational(memory), cognitively(interpretability) and statistically(large amount of data for parameter estimation).
- Compactness in terms of number of independent parameters
- More compactness lead to less expressiveness
- Naive Bayes model: assumptions and structure. Independence assumption lead to over-counting because single effect represented by highly correlated variables are considered multiple times.

## Bayesian Network

- Bayesian network: structure + conditional probability distribution(CPD)
- The `Student` Bayesian network which is representative(4 types of typical \\(X, Y, Z\\) relation)
- causal reasoning(cause observed), evidential reasoning(evidence observed) and intercausal reasoning(or explaining away, one cause of one effect can influence the other cause of the same effect)
- \\( \mathcal{I}_l(\mathcal{G})\\): local independencies encoded by \\( \mathcal{G} \\): variable \\(X\\) independent of non-descendants given parents of \\(X\\)
- I-map: \\(\mathcal{G} \\) is an I-map for \\(P\\) if \\( \mathcal{I}_l(\mathcal{G}) \subseteq \mathcal{I}(P)\\). There might be independencies not reflected in \\(\mathcal{G}\\)
- Factorization: \\( P \\) factorizes according to \\(\mathcal{G}\\) if \\( P \\) can be expressed as $$ \prod\limits_{i} P(X_i \mid Pa_{X_i}^{\mathcal{G}}) $$
- I-map and factorization are equivalent(**?**): a strong correlation between graph structure and independence assertions
- Knowledge engineering: picking variables, picking structures, picking probabilities

## Independencies in Graphs

- v-structure(\\( X \rightarrow Z \leftarrow Y\\)), active trial
- D-separation: \\(\text{d-sep}_\mathcal{G}(\mathbf{X}; \mathbf{Y} \mid \mathbf{Z})\\) if no active trials between any nodes in \\(\mathbf{X}\\) and \\(\mathbf{Y}\\) given \\(\mathbf{Z}\\)
- Global Markov independencies: \\( \mathcal{I}(\mathcal{G})\\) independencies indicated by D-separation.
- soundness of d-separation: if \\( X \\) and \\( Y\\) are d-separated given \\(Z \\), then they are conditionally independent given \\( Z \\). Equivalently, ( \\( \mathcal{I}(\mathcal{G}) \subseteq \mathcal{I}(P) \\))
- completeness of d-separation: d-separation detects *all* possible independencies( \\( \mathcal{I}(P) \subseteq \mathcal{I}(\mathcal{G})\\)). **Does not hold**
- However, for almost all \\( P\\) that factorizes over \\( g\\), ( \\( \mathcal{I}(P) = \mathcal{I}(\mathcal{G})\\)). d-separation can lead to independency however, it's not the other reason. The actual values in CPD can indicate independency as well. 
- A linear time algorithm(via BFS) that detects the reachable node from \\( X\\) given \\(Z\\) via active trials.
- I-equivalence: \\( \mathcal{K}_1\\) and \\( \mathcal{K}_2\\) are I-equivalent if \\( \mathcal{I}(\mathcal{K}_1) = \mathcal{I}(\mathcal{K}_2)\\)
- If same skeleton and same v-structures, then I-equivalent(sufficient but not necessary) and more theorems on that.

## From Distributions to Graphs

- Fundamental question: how to construct the \\(\mathcal{G}\\) that reflects the \\( \mathcal{I}(\mathcal{P})\\)?
- minimal I-map: the I-map of \\( P \\) such that removal of any edge will make it not an I-map. this is an approximation to \\( \mathcal{I}(\mathcal{G})\\)
- For the algorithm that finds minimal I-map: different variable ordering may produce different result. Even for the same ordering, this result may not be unique
- Perfect I-map: graph \\( \mathcal{K} \\) such that \\( \mathcal{I}(\mathcal{K}) = \mathcal{I}(\mathcal{P})\\)
- Two good examples that reflects the type of independencies that BN **fails to capture**: 1, independencies that are inferred from specific problems, 2, the *misconception* example

# Learned from video

- If you gain something(parametrization), you lose something(dependency). It's conversely true. representation cost vs representation power or expressiveness vs #parameters
- *Dishonest casino* example: evaluation(how likely is the sequence), decoding(what are the dices used for each toss), learning(how often does the casino player changes the dice and how loaded is the dice)
- Purpose of using pgm: transform a computation(margin/posterior proba) which can be exponential into something polynomial. For example, HMM
- Goal of BN: for \\( P \\) to be represented in a *factorized* way and represent *a set of conditional independence assumptions* about \\( P \\)
- Why do we say *independence* instead of *dependence*? Because *independence* s a safer assumption(**is that absolutely safe?**). For example \\( A \rightarrow B\\) does not guarantee \\(A \\) is independent of \\(B\\)(we can craft some numbers to make them dependent)
- Prove that the independence assumption by \\(P\\) is consistent with \\(\mathcal{I}(\mathcal{G})\\)
- I-map: connects graph with distribution
- Alternative definition of D-separation: separated in the *moralized ancestral* graph. Being *moral* means being connected(or married). Ancestral graph is the graph where the variables-in-question's  descendants are removed
- Graph can be an representation for probability distribution. As a result, in order for \\(\mathcal{G}\\) to be useful, the independence properties from it should also hold for \\(P\\).
- Refresher: I-map and factorization are equivalent
- D-separation guarantees soundness and almost "completeness"(exceptions are zero-measure distribution)

# Questions

- Why are factorization, i-map, d-separation and i-equivalence useful?
  Factorization and i-map are used to connect graph structure to conditional independence assumptions back and forth.
  D-separation: defines the criteria when \\(X\\) and \\(Y\\) are conditionally independent given \\(Z\\)


