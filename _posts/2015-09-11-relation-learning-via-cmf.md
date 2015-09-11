---
layout: post
title:  "Relational Learning via Collective Matrix Factorization"
date:   2015-09-11 14:58:23
categories: paper
tags: matrix-factorization  relation-learning collective-matrix-factorization 
---

# Relational Learning via Collective Matrix Factorization

[paper link](http://www.cs.cmu.edu/~ggordon/singh-gordon-kdd-factorization.pdf)

## Collective Matrix Factorization

Given two data matrices, \\(X\\), user to movie rating and \\(Y\\), movie to genre mapping, we wish to learn \\(U, V, Z\\) such that:

\\( \mathit{X} \approx f_1(UV^T)\\) and \\( \mathit{Y} \approx f_2(VZ^T)\\)

\\(V\\) is shared among different matrix factorization.

A stochastic approximation(sampling based etimation on gradient and Hessian) optimization method can be used to handle large, sparse matrices.

The method can be generalized to arbitary relational schemas.

## What I learned

- Collective Matrix Factorization to improve performance by simultaneously factorizing several matrices and sharing parameters
- The existence of Bregman Divergence as a general way for loss function in MF
- Weights can be used to rescale the loss function(large/small matrix, cope with missing values)


## Important/not-yet-understood topics

- How to interprese Newton optimization method? When can it be used? Why is it useful?
- How Newton method compared with gradient descent? More learning on convex optimization.
- The 'updating-each-row' argument in Section 4.1
- How stochastic optimization works?
