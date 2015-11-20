---
layout: post
title: "Resource: Wikification and Beyond"
date: 2015-11-04 18:28:22
categories: resource
tags: wkificiation entity-linking resource
---

# Mention detection

[Evaluating the Impact of Phrase Recognition on Concept Tagging](http://www.lrec-conf.org/proceedings/lrec2012/pdf/545_Paper.pdf) or P36

P40: Mention expansion, acronym mining(CCP, NBA, MINDEF), weighted finite state transducer

# Candidate entity generation

- substring/overlap based

Ranking entities: commoness, graph-based(centrality, PageRank), context-similarity measure

- [Learning Relatedness Measures for Entity Linking](http://dexter.isti.cnr.it/static/papers/cikm13.pdf)


# [Mendes, 2012, Evaluating the Impact of Phrase Recognition on Concept Tagging](http://www.lrec-conf.org/proceedings/lrec2012/pdf/545_Paper.pdf)

# [Li, 2014, Incremental Joint Extraction of Entity Mentions and Relations](http://www.anthology.aclweb.org/P/P14/P14-1038.pdf)

# [Huang, 2014, Collective Tweet Wikification based on Semi-supervised Graph Regularization](http://nlp.cs.rpi.edu/paper/tweetwikification.pdf)


# [Chen, 2011, Collaborative Ranking: A Case Study on Entity Linking](http://nlp.cs.rpi.edu/paper/clranking.pdf)

Focuses on the *candidate entity ranking* part. Candidate generation uses method from another paper.

Basic idea: query expansion and ensemble learning.

A query is triplet of *(query_id, name string, context document)*.

Two levels of collaborations(**a cool figure** in paper).

- Query-level: query expansion using similar queries
- Ranker-level: different ranker for final score prediction

Easter egg: three learning approaches for learning to rank: 1) pointwise, 2) pairwise 3), listwise. Need to read more.

Side notes:

- Weighted finite transducer(for acronyms, such as NBA).
- Entity linking on YAGO which has richer semnatics for relations.

# [Cassidy, 2012, Analysis and Enhancement of Wikification for Microblogs with Context Expansion](http://www.anthology.aclweb.org/C/C12/C12-1028.pdf)

# [Moro, 2014, Entity Linking meets Word Sense Disambiguation: a Unified Approach](http://wwwusers.di.uniroma1.it/~navigli/pubs/TACL_2014_Babelfy.pdf)

Global inference(graph approach)


P79: challenges
