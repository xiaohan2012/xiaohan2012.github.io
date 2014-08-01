---
layout: post
title:  "How Maui works with Wikipedia?"
date:   2014-07-25 16:01:41
categories: paper-reading
tags: maui wikipedia keyword-extraction nlp
---

[Maui][maui-google-code] is a key phrase indexing tool. One of capability is assigning key terms to documents. One option is let the task base on the knowledge of Wikipedia. This article talks about how Maui actually does this by use of Wikipedia.

Three steps, *Candidate generation*, *disambiguating* and *filter*, are performed.

## Candidate generation

Using keyphraseness(\#links in all Wikipedia pages/ \#occurrences in all Wikipedia pages) and a threshold value to be compared against to identifying important words and phrases

## Disambiguation

Disambiguating the phrases is necessary due to the ambiguous nature of natural language. So this step maps each phrase to one Wikipedia article, which represents one sense/concept.

First case-fold and stem all the phrases. If they happen to be the name of an article, use it as the sense. If the phase leads to redirect page. Then use the target article as the sense.

Interesting happens when it leads to disambiguation pages, the intended sense needs to be determined.

Commonness and semantic relatedness is used.

- *Commonness*: To what extent the sense T is well known as the phrase S
- *Semantic relatedness* in terms of the context: context refers to the set of senses unambiguous and relatedness computation of two senses is based on the number of shared in-going links to the two sense.

## Filtering

Classifying the candidate article names as being key phrase or not

Using several features to classify each article name individually.

Features:

- TD-IDF based
- first/last occurences.
- spreadness: last occur position - first occur (How about some measurement similar to Kurtosis)
- total keyphrases of article name `A` in document `d`: sum of keyphrasesness of the phrase `p` that maps to `A` times the frequency of `p` appearing in `d`.
- semantic relatedness: 

[maui-google-code]: https://code.google.com/p/maui-indexer/ "Maui host at google code"