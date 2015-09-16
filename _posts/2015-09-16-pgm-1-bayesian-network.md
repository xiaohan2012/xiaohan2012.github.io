---
layout: post
title: "PGM course: Bayesian Network"
date: 2015-09-16 15:58:13
categories: course
tags: pgm bayesian-network
---

# Learned

## Exploiting Independence Properties

- Why represent the joint probability in a compact way: computational(memory), cognitively(interpretability) and statistically(large amount of data for parameter estimation).
- Compactness in terms of number of independent parameters
- More compactness lead to less expressiveness
- Naive Bayes model: assumptions and structure. Independence assumption lead to overcounting because single effect represented by highly correlated variables are considered multiple times.

## Bayesian Network

- Bayesian network: structure + conditional probability distrbution(CPD)
- The `Student` Bayesian network which is representative(4 types of typical \\(X, Y, Z\\) relation)
- causual reasoning(cause observed), evidential reasoning(evidence observed) and intercausaul reasoining(or explaining away, one cause of one effect can influence the other cause of the same effect)
- \\( \mathcal{I}_l(\mathcal{G})\\): local independencies encoded by \\( \mathcal{G} \\): variable \\(X\\) independent of non-descendants given parants of \\(X\\)
- I-map: \\(\mathcal{G} \\) is an I-map for \\(P\\) if \\( \mathcal{I}_l(\mathcal{G}) \subseteq \mathcal{I}(P)\\). There might be independencies not reflected in \\(\mathcal{G}\\)
- Factorization: \\( P \\) factorizes according to \\(\mathcal{G}\\) if \\( P \\) can be expressed as $$ \prod\limits_{i} P(X_i | Pa_{X_i}^\mathcal{G}) $$
- I-map and factorization are equivalent(**?**): a strong correlation between graph strcture and independence assertions
- Knowledge engineering: picking variables, picking structures, picking probabilities

## Independencies in Graphs

- v-strcuture(\\( X \rightarrow Z \leftarrow Y\\)), active trial
- D-separation: \\(\text{d-sep}_\mathcal{G}(\mathbf{X}; \mathbf{Y} | \mathbf{Z})\\) if no actial trials between any nodes in \\(\mathbf{X}\\) and \\(\mathbf{Y}\\) given \\(\mathbf{Z}\\)
- Global Markov independencies: \\( \mathcal{I}(\mathcal{G})\\) independencies indicated by D-separation.
- soundness of d-separation: if \\( X \text{and} Y\\) are d-separated given \\(Z \\), then they are conditionally independent given \\( Z \\). Equivalently, ( \\( \mathcal{I}(\mathcal{G}) \subseteq \mathcal{I}(P) \\))
- completeness of d-seperation: d-separation detects *all* possible independencies( \\( \mathcal{I}(P) \subseteq \mathcal{I}(\mathcal{G})\\)). **Does not hold**
- However, for almost all \\( P\\) that factorizes over \\( g\\), ( \\( \mathcal{I}(P) = \mathcal{I}(\mathcal{G})\\)). d-separation can lead to independency however, it's not the other reason. The actual values in CPD can indicate independency as well. 
- A linear time algorithm(via BFS) that detects the reachable node from \\( X\\) given \\(Z\\) via actial trials.
- I-equivalence: \\( \mathcal{K}_1\\) and \\( \mathcal{K}_2\\) are I-equivalent if \\( \mathcal{I}(\mathcal{K}_1) = \mathcal{I}(\mathcal{K}_2)\\)
- If same skeleton and same v-structures, then I-equivalent(sufficient but not necessary) and more theorems on that.


# Questions

- Why are factorization, i-map, d-separation and i-equivalence useful?

