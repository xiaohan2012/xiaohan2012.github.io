---
layout: post
title: "Rhythms of Information Flow through Networks by Jure Leskovec"
date: 2015-10-05 23:52:55
categories: tutorial
tags: information-diffusion meme-tracking temporal-pattern social-media network-inference
---

## Topics

- Meme tracking in news media
- Temporal patterns: what are the different types of temporal patterns
- Predicting the influence(time series analysis): how many sites will report this news?
- Inferring latent information diffusion network: how information is spread in the implicit network


## [Cascading Behavior in Large Blog Graphs Patterns and a model](https://cs.stanford.edu/people/jure/pubs/blogs-sdm07.pdf), SDM 07

Patterns of information propagation  --> how information(rumor, idea) spreads.

Contribution:

- temporal patterns: no bursty behavoir and post popularity drops with power law(**?**)(instead of exponentially)
- topological patterns: the star shape is most popular
- generating model: some model that mimics the cascading effect

## [Meme-tracking and the Dynamics of the News Cycle](https://www.cs.cornell.edu/home/kleinber/kdd09-quotes.pdf)

What to track? Phrase, tags, links, tags, quotes, images, bar codes..

In this case, quotes.

How to identify variants of quotes?

- Create DAG by adding edges between quotes if one contains the another as a substring(up to some edit distances)
- Apply heuristic-based graph partitioning to cluster them

Synchronization

- Are blogs ahead or trailing of main stream media?

**Q**: Why use quote? Why not use phrase?


## [Patterns of Temporal Variation in Online Media](https://cs.stanford.edu/people/jure/pubs/memeshapes-wsdm11.pdf)


Patterns of information attention. the shape of of information piece's volume over time. How it gains popularity and loss popularity.

-  What classes of shapes?

Clustering time-varying volume shapes. Properties of similarity metric: 1, invariance to scale, 2, invariance to translation(offset)

K-means doesn't work. **Why?** . Instead, k-spectral centroid clustering works.

Different types of media give different patterns.

## [Modeling Information Diffusion in Implicit Networks](http://cs.stanford.edu/people/jure/pubs/lim-icdm10.pdf)

Information attention prediction(classical time series prediction problem).

Basic question: given who and when the information is reported, how many sites will mention this later on.

Use influence function: how many sites mention this information after this site mentions it in correlation with this site?

How to estimate the influence function?

- Discretize each function as a vector(size 24 for example)
- Use least square to solve

Compared to standard time series regression, AR and ARMA. How do they work and why this model is better?

**Q**: modeling it in explicit network?

## [Inferring Networks of Diffusion and Influence](http://dl.acm.org/citation.cfm?id=1835933)

How does the information really spread?

Examples:

- virus propagation: only know who and when got inflected. But don't know who inflected whom
- word of mouth: only see who bought what and when but don't know who influenced whom 

\\(P(G \vert C) \\): how possible is \\(G\\) the generating graph for the inflections \\(C\\)? How to compute this quantity? How to find the one corresponding to maximum likelihood

Intuition: if two nodes get inflected at two points of time quite close to each other, then there is probably an edge between the two nodes.

Information diffusion process betwen two nodes:

1. flip a biased coin
2. sample some delay time(or incubation time)

Consider only trees(no loop). NP-hard(max-k-cover). can use greedy hill climbing, at each iteration, add the edge that gives the most improvement to the score.

Experiment:

- Simulated data: better precision/reclal curve than baseline
- On real data: topical cluster(political, entertainment, tech) as well as node position in the graph which indicates their topical preference.

## Further questions

- Sentiment change
- Extensions to other domains: epidimiology, viral marketing

## Resrouces/links

- [2011 talk by Jure](http://videolectures.net/eswc2011_leskovec_flow/)
- [SNAP by Stanford](http://snap.stanford.edu/)
- [Dataset by Snap](http://snap.stanford.edu/data/index.html)
- [Mining Massive Data Set book/course](http://www.mmds.org/)


## Related question for email network

- Meme tracking: track what I have been doing and at what involvement.
  - What is the meme for this case? How to extract the meme?
  - Maybe categorize the flow by event type, for example conference submission/course/research etc. How to define the event?
- Temporal pattern: what are the temporal patterns for events in user's email? 
  - How to define the pattern? Response time/rate to different users? volumes of emails to certain users at different time points? Why do we study this problem?
  - Can we categorize those patterns? The simplest case, we just don't reply(conference calls, promotion, etc). Or some short reply to stranger? Long-term and frequent communication with some cooperator or relatively short term communication with a course staff?
- How information flows in email network? Or organizing the internal "logic" of the email timelines.
  - Why do we bother it? What do you mean by "information flow"? Can we find other information flow patterns besides those indicated by the staff network structure(this is not for ego network)?
  - For ego network, can we model how two different emails are correlated with each other or to what degree are they correlated. For example, one mail might be a simple extension of the work mentioned in another mail. Or some progress reporting based on some previously assigned task.
- Email timeline chaining/segmentation: can we chain the emails so that those in the same story line are in the same chain? Potentially useful for email organization or retrospection.
  - Is this like a special clustering problem where two emails might be in the same chain if they share similar topic, share common recipients and their timestamp are "reasonable"(impossible that two emails whose timestamp are within 1 sec are in the same line)
- What kind of information can we leverage for more efficient email using or less cognitive/mental load for the user?
  - What causes the load/inefficiency? Too many emails? 
  - Grouping/sorting emails? Hide unnecessary/trivial emails?
  - Need some reading on cognitive science?


## Learned

- What does it mean by temporal patterns and one way/metric to cluster different patterns
- In meme tracking, a new way to clustering/partitioning quotes by adding edges(considering substring edit distance)
- Time series prediction method: AR, ARMA and via the influence function
- A new problem: modeling how information is spread and method to approach it

