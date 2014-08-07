---
layout: post
title: "Graph based keyword/sentence extraction"
date:   2014-08-07 14:00:00
categories: paper-reading
tags: keyword-extraction sentence-extraction graph graph-algorithm page-rank
---

##Introduction

The blog introduces how to perform keyword/sentence extraction using graph algorithm similar to Page-rank.


##Key idea

We can see text as a graph where the vertices can be phrase, sentence, even single word. Just like the way human sees text as a network of interconnecting concepts.

To define the edges we need some kind of relation between the text units.

Then we can run page-rank algorithm to rank the text units.

If we define the unit to be phrases, the task is actually keyword extraction.

This approach is proposed by [paper][paper]


##Advantage

1. unsupervised
2. easy to implement


##Example


###Keyword extraction
Individual words are considered vertices and co-occurrence is used as relate one word to another.

Post processing: we can combine top ranked words into one, if they form phrase in the text.

###Sentence extraction
The degree to which two sentences are connected is the ratio of words that they share.


##Problem & Improvement(for keyword extraction)


###Single-occurrence word issue:

Some keywords occurs only once and are not likely to be ranked high.

Possible causes of the problem:

####Synonym

The keyword is just another (unusual) form of saying the same thing, for which is already mentioned in the text several times. 

For example, *North Atlantic Treaty Organization* is mentioned only once. And later, it is abbreviated as *NATO* many times.

Possible solution:

- Semantic analysis: mapping phrases, like NATO, to Wikipedia concepts, like it full name.

####Reference

For example, Uncle Sam is mentioned once at the beginning. Later, it is referred as *he*.

Possible solution:

- Reference resolution technique: mapping *he* to *Uncle Sam*.


[paper]: http://www.cse.unt.edu/~rada/papers/mihalcea.emnlp04.pdf