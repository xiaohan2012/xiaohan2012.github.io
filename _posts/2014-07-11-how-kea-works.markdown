---
layout: post
title:  "How KEA works?"
date:   2014-07-21 16:01:41
categories: paper-reading
tags: kae keyword-extraction nlp
---

Paper link is [here][paper]. Published in 1999, with software written and open-sourced.

Two major steps are involved: *identify candidate phrases* and *classifying the candidates*.

##Identify candidate phrases

###Preprocessing

Cleaning, replacing punctuation, etc with phrase boundary, resulting a list of sequences of words

Example:

Before cleaning

- authors are unlikely to use—for example, gauge, smooth, and especially garbage

After cleaning:

- authors are unlikely to use
- for example
- gauge
- smooth
- and especially garbage

###Phrase identification

Using stop words to separate the words sequences into phrases

Given stop words to be `are`, `to`, `and`

will result in:

`authors`, `unlikely to use`, `for example`, `gauge`, `smooth`, `especially garbag`.

###Stemming and case-folding

For example:

    authors -> author
    Gauge -> gauge

##Classifiy the candidate phrases as key or not

A Naive Bayes classifier is used to classify the candidate  phrases as key phrase or not.

Two features are used in the model:

- *Tf-idf of the phrase*: the document frequency is calcualted using a global corpus
- *First occurence of the phrase*: number of words that occure before the first occurence of the phrase / number of words in the document

However, a Naive Bayes requires discrete feature values, discretization is used. NOTE: supervised discretization method is used here.

###Traning of the model

Given a set of documents with key phrases annotated as the training set, the candidate key phrases are extracted and marked as 1, if it is a key phrase and 0, otherwise.

Features are calcualted and discretized and used for training.


###Prediction

The Naive Bayes prediction sutff.

## My thought

###Highlight of the paper:

- Ways to identify candidate key phrases can even be applied to Chinese.
- Really simple to understand 

###Some possible improvement:

- more features can be added
- key phrases not existing but convey the key message of the document can not be extracted. So how to solve it?


[paper]: http://arxiv.org/ftp/cs/papers/9902/9902007.pdf  "Paper here"