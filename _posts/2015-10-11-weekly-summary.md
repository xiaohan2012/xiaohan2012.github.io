---
layout: post
title: "Weekly Summary, Oct, 11, 2015"
date: 2015-10-11 11:04:37
categories: weekly-summary
tags: weekly-summary
---

# Done & learned

1. Followed [Rhythms of Information Flow through Networks](http://xiaohan2012.github.io/tutorial/2015/10/05/rhythms-of-information-flow-through-network), learned several interesting problems/models:
   - meme tracking: quote clusterin + the streaming graph
   - clustering temporal patterns/graph and a similarity that copes with different scaling and offset issues
   - Time series prediction(information attention prediction) using the influence function based model
   - Inferring information propagation/diffusion graph
2. Read one paper [Dynamics of Personal Social Relationships in Online Social Networks: a Study on Twitter](http://xiaohan2012.github.io/paper/2015/10/07/dynamics-of-personal-social-relationship-in-online-social-network/), learned several seemling trivial problems, which turn out to be non-trivial and interesting. For example:
   - How long do people use Twitter? What are the patterns or user category?
   - How does the number of alters and actively contacted alters change over time(ego network evolution)?
3. Tried to add graphviz support to my blog but failed. Learned:
   - Never be addicted to the fancy stuff and spend too much time on trying to get it right. Time to let it go...
4. Discussed with Aris and Polina about the master thesis topic
   - Narrowed it down to: graph summary for dynamic labeled graph or labeled user interactions.
   - Core issue: select a subset of edges(interaction records) and group them by satisfying several constraints such as coverage, density, topic relatedness and time homogeneity.
   - Tried to formulate this problem as a optimization problem that combines objective functions of several aspects
   - Related this problem to other problems such as graph partitioning

# Todo

- Read more on dynamic graph partitioning
- Discuss with Polina and about I want to do
- Discuss with Aris about my ideas


