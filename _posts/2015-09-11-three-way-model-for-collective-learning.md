---
layout: post
title:  "A Three-Way Model for Collective Learning on Multi-Relational Data"
date:   2015-09-11 21:32:38
categories: paper
tags: tensor-factorization  relation-learning RESCAL
---


# A Three-Way Model for Collective Learning on Multi-Relational Data

[Paper link](http://www.cip.ifi.lmu.de/~nickel/data/paper-icml2011.pdf)

## Summary

An efficient algorithm for relation learning based on entity-relation-entity tensor factorization.

Given 3D tensor \\(\mathcal{X}\\) with binary value, where \\(X_{ijk}\\) denotes whether there exists a fact that involves `(i-th entity, k-th relation, j-th entity)`, find the low-rank factorization \\(A\\) and \\(R_k\\) such that

$$\mathcal{X}_k \approx A R_k A^T$$, where \\(\mathcal{X}_k\\) is the slice for the kth relation.

Training objective:

$$f(A, R) = \frac{1}{2}\left\| \mathcal{X}_k - A R_k A^T \right\|^2_F +  \frac{1}{2} \lambda (\left\| A \right\|^2_F + \sum\limit_k\left\| R_k \right\|^2_F )$$

which is squared error + L2 regularization.

The error term can be expressed as matrix operation once stacking the tensor variable.

Parameter estimation:

*Alternating least-squares*: alternatively optimize either \\(R\\) or \\(A\\) which fixing the either.

For \\(A\\)  which appears at both sides of $$A R_k A^T$$, we can fix one side of \\(A\\) and solve the other one alternativley.


## What I learned

- There are multiple ways to factorize 3D tensor. In this paper(`RESCAL`) \\(A R_k A^T)\\. In `DEDICOM` \\(A D_k R D_k A^T)\\, where \\(R\\) is global across relations(more restrictive)
- ALS as parameter estimation method for the tensor factorization problem.
- Collective classification where prediction is made (for one relation) collaborative using other data(from other relations). See the experiment part
- Entity resolution by using the entity embedding as representation
- chord graph as a way to visualize entity entity connection/relationship strength
- [scikit-tensor](https://github.com/mnick/scikit-tensor) includes RESCAL

## Questions

- Using PBR to train?
- How to scale up? The series of matrix multiplication seems expensive
- For each \\(k\\), \\(R_k\\) is independent. How to aggregate/connect them? Also, can we connect those entities?

