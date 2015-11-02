---
layout: post
title: "Entity Linking Literature Review"
date: 2015-11-02 17:25:25
categories: review
tags: entity-linking
---

[WSDM 2014](https://www.dropbox.com/sh/ekzqc3mdk4qkdlw/Egdq-WfUa7#)

- Entity Linking
- Entity Retrieval



# [Milne 2008, Learning to Link with Wikipedia](http://www.cs.waikato.ac.nz/~ihw/papers/08-DNM-IHW-LearningToLinkWithWikipedia.pdf)

Steps:

1. Find a set of candidate terms and DA(disambiguation) on them
2. Using features dervied from 1, LD(link detection) on those disambiguated terms to decide whether the term should be linked or not.

Key idea: DA informs LD.

Can be effectively for lone text but not necessarily short ones, in which context information becomes less, thus unreliable.

## DA

A binary classifier with three features, whose input is a term and a Wikipedia article and output the probability that the term should be disambiguated as such. Three features are used:

  - commonness(popularity)
  - weighted sum of relatedness(to its context terms)
  - quality of context: sum of second feature

Training data: links in Wikipedia articles as positive.

## LD

A binary classifier is used to predict the proba that a disambiguated term should be linked/selected.

Use Wikipedia articles to train the classifier. Positive(negative) examples: terms that are(aren't) linked.

Features include: max/mean link probability, max/mean of relatedness, disambiguation confidence(result from DA), generality(article depth in the Wikipedia concept tree), position and spread.

# [Robust Disambiguation of Named Entities in Text](http://aclweb.org/anthology/D/D11/D11-1072.pdf)

short text. collectively DA as a graph problem. robustness by selectively using components.

prior, similarity, coherence

A football game between "Manchester" and "Barcelona" that takes place in "Madrid".


coherence graph problem:

compute a subgraph with maximum density(?)
