---
layout: post
title: "MMDS: community detection"
date: 2015-10-11 22:51:25
categories: course
tags: graph-mining community-detection spectral-clustering
---

# Topics

## Affiliation Graph Model and BigCLAM

For overlapping community detection. Can be seen as matrix factorization.

Each node represented by a vector of community membership(length k, where k is the number of communities), \\(U_i\\)

Minimize the log likelihood:

$$ \sum\limits_{(i, j) \in E} \log p(i, j) + \sum\limits_{(i, j) \not\in E} \log(1- p(i, j)) $$

where \\(p(i, j) = 1 - \exp(-U_i^T U_j)\\)

## Application

Mining:

- social/transaction/protein network to detect community
- query-advertiser network to detect market
- movie-actor network for interesting patterns

## Conductance

conductance of a set of nodes = cut / volume

The lower the value, the more "community-like" the node set is.

## Spectral graph partitioning

Spectrum: eigen values/vectors of graph

d-regular graph and graph with two d-regular component sub-graphs.

eigen vector gives information on community membership information and eigen value gives information on the average degree.

Laplacian matrix: degree matrix - adjacancy matrix.

trivial eigen pair, eigen vector values all 1 and eigen value being 0.

**Keynote**: min cut is equivalent to the 1973 problem, which corresponds to minimizing the squared loss function, whose answer is given by eigen value decomposition of the Laplacian matrix.

## Finding bipartite subgraph

The subgraph is fully connected.

Usercase: community finding in Web graph(Hubs link to authoritie)

Can be converted to frequent itemset finding.


## Mining stream data

Compared to RDBS, streaming data is characterized by:

- rapid data flow whose speed controled by external factor(Google search query)
- system cannot store the entire stream

Possible application:

- mining search query to get the hot trend(Google Trend)
- mining user link click to get hot news
- mining packet flow on router to optimize routing strategy

Human brain is essentially processing many kinds of streaming data(sound, light, smell, etc)

Why sliding window?

The Aalto summer internship question of 2014 summer.

## Bloom filter

Web crawler: throws away the URLs that have been crawled before.

How to test if one URL has been crawled before?

Bloom filter characteristics:

- always correct about claiming some URLs to be "unseen"(100% precision)
- might drop some "unseen" URLs(< 100% recall)

Consists of an bit array(initially zero) and a list of hash functions.

For each stream element, compute the hash values. If at least one of the corresponding positions in the bit array is zero, the element is claimed unseen. Otherwise, it's seen. After that, set the corresponding bits in the array to 1.

Questions:

- Choices for the hash function? How many functions? 
- Why it works? 100% precision and <100% recall?


Performance:

Probability of some bit remaining zero: \\(p_0 = \exp (-d/t)\\), where \\(t\\) is the number of bits in the bit array and \\(d\\) is the number of bits inserted so far.

Density of 1s: \\(p_1 = 1 - p_0 \\). Probability of claiming "seen": \\(p_1^{\text{#hash_functions}}\\)


