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

# [Hoffart 2011, Robust Disambiguation of Named Entities in Text](http://aclweb.org/anthology/D/D11/D11-1072.pdf)

## Objective

$$ argmax\limits_{e_{1 \ldots k}} \alpha \sum\limits_{i=1\ldotsk} prior(m_i, e_i) + \beta \sum\limits_{i=1\ldotsk} sim(cxt(m_i), cxt(e_i)) + \gamma coh(e_1 \ldots e_k) $$

s.t.

$$ \alpha + \beta + \gamma = 1$$

$$ e_i \in cnd(m_i) $$

- Use Stanford NER to detect noun phrases
- Prior probability for entity: "Kashmir" refers to Kashmir (the region) in 90.91% of all occurrences.
- Similarity between mention and entity. **Keyphrases** of entity as the bridge between entity and mention.
  - keyphrases of entity, \\( KP(e) \\): anchor text that links to it and titles of articles that link to it.
  - each word in keyphrase has a score/weight with the entity, can be PMI or IDF
  - for each keyphrase, \\(q\\)(which partially matches the context), a score, \\(score(q)\\) is calculated based on the "cover" of the keyphrase in the mention context. Definition in paper. How the cover is computed(shortest window of words that contains a maximal number of words of the keyphrase)?
  - \\( simscore(m, e) = \sum\limits_{p \in KP(e)} score(q)\\)
  - syntax-based similarity: more in paper(Thater10)
- Coherence score among a set of entities: the coherence score between a pair of entities uses "relatedness" in (Milne08)

## Graph problem

The above optimization problem can be converted to a graph problem. The graph consists:

- two types of nodes: mention and entity weighted by similarity between mention and entity or *optionally* combined with the popularity prior.
- two types of edges: mention-entity and entity-entity

Dense subgraph detection(**Wow!**): find the *dense* subgraph that covers all mention nodes and for each mention node, there is an edge to one entity.

*density* definition: minimum of weighted degree among all its nodes(total weight of all incident edges of a node). To cope the long-tail problem(less prominent and sparsely-connected entities)

NP-hard prblem. Approximate algorithm:

- prune entites that are remotely related to mentions(resulting in a smaller graph)
- iterately remove the edge that has the least weighted degree without violating the structure constraint. Track the best subgraph
- return the best subgraph


## Robustness tests

Cope with short text and thematically heterogeneous.

**prior test**: For short text, the result can be dominated by a few false alternatives with relatedly high prior(say, <= 90%). In that case, we ignore it. This is checked for each mention. **Key idea**: for short text, context might matter more than prior(fixed).
**coherence test**: for each mention, if the disagreement between prior and similarity is big, we use coherence to solve the conflict. Otherwise, we'd better ignore it because if the text is thematically heterogeneous, coherence score(low) will damage.

Very clever.
