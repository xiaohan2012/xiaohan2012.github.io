---
layout: post
title: "Overlapping Community Detection in Labeled Graphs"
date: 2015-10-08 22:03:41
categories: paper
tags: graph-mining labeled-graph community-detection approximation-algorithm
---

# Problem Definition

Goal: finding dense communities that can be succinctly described by label set

Input:

- label set \\(L\\)
- graph \\(G = (V, E,\mathcal{l})\\), where \\(V\\) is vertex set, \\(E\\) the edge set and \\(l\\) mapping from vertex to label subset: \\(V \rightarrow 2^{\vert L \vert}\\)
- \\(k\\), the number of communities we want to detect

Output:

- list of label set, \\(S_i, \ldots S_k\\), where \\(S_i \in L\\)
- list of *disjoint* edge set,  \\(F_i, \ldots F_k\\), where \\(F_i \in E\\)

so that that resulting list of subgraphs \\(H_i = (p(S_i), F_i)\\) maximizes the density:

$$ d(H_i, \ldots, H_k) = \sum\limits_i d(H_i)$$

where \\(d(H_i) = \frac{2 \vert F_i \vert}{\vert p(S_i) \vert }\\), which is the average degree of \\(H_i\\). \\(p(S_i)\\) is the set of vertices that satistify the predicate \\(p\\) associated with \\(p\\)(a little unclear though...)

**Questions**:

- Why do we implicitly/indirectly select the vertices by selecting the labels first and then use \\(p(S)\\) to induce the vertex set? Why not the reverse order? Select the vertices first and then induce the label?
- Instead of using "hard" predicate, can we use some "soft" predicate that gives some score/probability?

# What if only edges are labeled?





# Approach


## Unlabeled graph

## Labeled graph


