---
layout: post
title:  "Relation"
date:   2015-09-10 17:33:13
categories: paper
tags: relation-extraction matrix-factorization bayesian-personalized-ranking
---

# Relation Extraction with Matrix Factorization and Universal Schemas

Paper [link](https://people.cs.umass.edu/~lmyao/papers/univ-schema-tacl.pdf)

## Motivation

"Relation extraction"" here is essentially "relation prediction".

The problem at hand is:

Given a matrix of facts, \\( \mathcal{O} \\) where rows are (entity, entity) tuples, \\(t\\) and columns are relations, \\(r\\), how to make predictions on the missing entries? For example, is `FUGURSON a-professor-at HARVARD` True given `FUGURSON is-historian-at HARVARD`?

## Approaches

For each fact, \\( (t, r) \\), its probability is measured by

$$ P(y_{t,r} = 1 | \theta_{t,r}) = \frac{1}{1+exp(-\theta_{t,r})}$$

where \\(\theta_{t,r} \\) is parameter for each \\((t,r\\) combination and it can be constructed in different ways and later summed up to from a total compatibility score.

In this paper, three sub-models for \\(\theta_{t,r} \\) are used.

### Universal Schema

Different schemas(list of relations) are concatenated to form a universal one, without further processing those relations(like relation clustering).

### Three submodels

- Latent feature model

Matrix factorization in the following ways:

$$ \Theta = (\theta_{t,r}) \approx = \mathbf{A} \mathbf{V}^T $$

In other words:

$$ \theta_{t,r} = A_{t,}\dot V_{r,}^T $$

where \\(A_{t,}\\) is the feature vector for the tuple \\( t\\) and  \\(V_{r,}\\) the feature vector for relation \\( v \\).

This model alone achieves better result than a state-of-the-art distant supervision model.

- Neighborhood model

The confidence of a tuple and a relation can be given by the confidence scores between similar relations for the same tuple.

$$ \theta_{t,r} = \sum\limit_{((t, r^{'}) \in \mathcal{O} \\ {(t,r)} w_{r,r^{'}})}


\\( w_{r,r^{'} \\) is the parameter to be estimated.

*Note*: this is also a type of matrix factorization.

*Q*: Vector similarity between \\(r\\) and \\(r^{'}\\) should also account for \\( w_{r,r^{'} \\).

- Entiy model

Each entity and each argumetn slot of each relation has embedding.

$$ \theta_{t,r} = \sum\limit_{i=1}^{arity(r)} r_{i,} \dot t_{i} $$

- \\( r_{i,}\\): the embedding for the \\(ith \\) slot of \\(r\\)
- \\(t_{i}\\): the embedding for the \\(ith\\) element of tuple \\(t)\\

Finally, the three scores are summed.

### Training

Bayesian Personalized Ranking is used to construct the objective function. Stochastic Gradient Descent is used for parameter estimation. At each iteration, one ranked pair is randomly sampled and used to calculate the gradient.

## Connection with other types of methods

### Distant Supervision(DS)
Uses KB to "label" the text corpus. Requires KB of the desired schema.

Relation surface form is mapped to schema of KD.

Here relations can be free text form. So the number of relations is greater, thus more expressiveness. Also, less compactness or more sparsity?

### OpenIE

It's only for extraction from textual data, not prediction.

### Relation clustering

Don't quite understand

## What I Learned

- Matrix factorization in the general sense(other than the \\( A = UV \\) case). Two more ways to factorize a matrix
- Matrix completion problem in the context of implicit feedback can be formulated as a ranking problem instead of prediction problem.
- The training method(*Bayesian Personalized Ranking*) is quite similar to that in the [personalized entity recommednation paper](/paper/2015/09/06/paper/)
- Relative simple way for relation extraction compared to other methods(distant supervision which uses graphical model)
- At each iteration during SGD, one ranked pair is randomly sampled and only part of parameter is updated.
- Relation clustering is used in relation extraction(**for what purpose?**)


## Questions

- Given unseen tuples, even member appears in the training tuples, is it possible to predict their relations?
- Can model matrix completion for entity typing problem? Or even collective matrix factorization(multiple matrices((entity mention, type), (eneity mention, relation), (relation, type)))?
  How are the collective matrix factorization done? When is it applicable?


## This paper is based on

- Michael Collins, Sanjoy Dasgupta, and Robert E. Schapire. 2001. A generalization of principal component analysis to the exponential family. In Proceedings of NIPS
- Yehuda Koren. 2008. Factorization meets the neighborhood: a multifaceted collaborative filtering model. In Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining, KDD ’08
- Steffen Rendle, Christoph Freudenthaler, Zeno Gantner, and Lars Schmidt-Thieme. 2009. Bpr: Bayesian personalized ranking from implicit feedback. In Proceedings of the Twenty-Fifth Conference on Uncertainty in Artificial Intelligence, UAI ’09

