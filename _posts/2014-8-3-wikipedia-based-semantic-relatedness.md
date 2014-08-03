---
layout: post
title: "Computing Semantic Relatedness using Wikipedia-based Explicit Semantic Analysis"
date:   2014-08-03 17:20:00
categories: paper-reading
tags: wikipedia semantic-analysis semantic-relatedness word-sense-disambiguation
---


##Problem to solve
Using information from Wikipedia to compute the semantic relatedness between:

1. Word and word
2. Text and text

##Highlight of the paper

1. It exceeds state-of-art performance prior to its time.
2. It is *explicit*, rather than latent in the way that human can  the *underlying* representation of word and text.

##How to solve

The method is termed as Explicit Semantic Analysis due to the use of *article headline*, or *concept* in Wikipedia to represent words and text. In short, word is represented by a weighted vector of Wikipedia concepts and text as a weighted vector summation(vector weighted by word TD-IDF value) of all the  word vectors derived from the text.

Word-word and text-text similarity can be calculated using any distance metric. Cosine distance metric is preferred as it calculates the angle similarity, which is preferable for text of diverging length.

###Word as concept vector

Concept vector for each word can be calculated using TF-IDF schema. The model is essentially the same as the document-word TD-IDF calculation. Each Wikipedia concept/article has a unique page with words in it. Thus, each concept can be represented using word vector.

###Text as concept vector

Text consists of words, each of which has a concept vector. Thus, the concept vector is simply the vector summation of the vectors of its containing words.

##Practical consideration
When deriving the concept vector for words, the following preprocessing is applied:

1. word stemming, stop-word/rare-word removal is applied
2. overly specific concept/article(too few words, too few incoming/outgoing links) is removed from consideration.

##Why this approach/advantage

1. In contrast to Latent Semantic Analysis, it is *explicit* for words/text are represented by **explicit** Wikipedia concepts. Thus it is easier to interpret.
2. According to observation, word sense disambiguation can be accomplished implicitly. Given the phrase, *Bank of America* and *Bank of Amazon*, in which *bank* are actually two totally different concepts, *bank* can be disambiguated. Thanks to the context information, a voting scheme involving all the words in the phrase manages to disambiguation the pivotal word, *bank* in the case.
3. It uses information encoded in Wikipedia, which is vast and rich in knowledge.

##Roaming thinking-word sense disambiguation by vector projection

In this paper, word sense disambiguation is achieved by *summing up* the vectors from contextual words, which resembles the scenario of voting. In this scenario, a list of words, each with its associated concepts, votes on the concepts and finally come up with a ranked list of concepts in terms of their association.

However, since we can think of a word as a vector in the high dimensional space of concepts, can we **project** a word into the context so that irrelevant concepts can be denoised?

In addition, the drawbacks of the word sense disambiguation approach in this paper is, it considers also the concepts in other words so that concepts from there might over-emphasize the intended concepts. For example, for the phrase, *Bank of China*, if we want to disambiguate *bank* using the summation approach, *China* might pop up as the first relevant concept.
