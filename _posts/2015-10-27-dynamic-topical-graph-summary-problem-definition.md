---
layout: post
title: "Dynamic topical graph summary: problem definition(2)"
date: 2015-10-27 17:07:31
categories: crafting
tags: graph-summary
---


# Problem Definition 1

Given the event graph, we induce a new graph where the nodes are the interactions and there is an directed edge from interaction \\((u_1, v_2, \alpha_1, t_1)\\) to \\((u_2, v_2, \alpha_2, t_2)\\) if \\(u_1 = u_2 \\) or \\(v_2 = u_2\\).

Then the original interaction graph, \\(G\\) is converted into a tree, \\(T\\).

Denote \\(E(G)\\) as the edge set contained in graph \\(G\\).

Our goal is:

Find \\(K\\) non-overlapping sub-trees of \\(T\\)

$$ T_1 \ldots T_K $$

such that

$$ \sum \vert E(T_i) \vert $$

is maximized under the constraint that

1. \\( coherence(T_i) > A \\)
2. \\( user_purity(T_i) > B \\)

\\( user_purity(T_i) \\) is the entropy function. It contrains that the majority of the interaction is carried out only by a few individuals in \\(T_i\\).

## Assumptions

1. An event can be represented by a subtree
2. An event is carried out by a few key users, which reflects the Constraint 2.
3. Topic tends to shift/change as time goes by

## Benefit of this model

1. Tree structure is a intuitive representation of how information flows
2. There is no need to add time constraint because under Assumption 3, extremely long event probably violates Contraint 1.
3. Can be extended to interaction network where more than 2 nodes are allowed to involve in one interaction. For example, the Bllomber entity network.

## Greedy randomized algorithm

1. Randomly sample a pool of seed nodes
2. For each seed, grow it by adding adjacent edges greedily(by topic coherence or user purity) until the constraint is violated. Then, we have a pool of subtrees
3. Greedily select the subtree that has the maximum number of edges and does not overlap edges with previously selected subtrees until we have collected \\(K\\) of them.

# Problem Definition 2

Given interaction graph \\(G=(V, E, \beta)\\), find \\(K\\) subsets of \\(V\\), \\(S_i, \ldots, S_K\\) and \\(K\\) sets of disjoint sets of edges, \\(F_i, \ldots, F_K \\) such that

$$ \sum \vert E(F_i) \vert $$

is maximized

Under the constraint that:

1. For \\( (S_i, F_i) \\): each node in \\(F_i\\) is in \\(S_i\\) and each node in \\(S_i\\) is covered by at least one edge in \\(F_i\\)
2. \\( coherence(F_i) > A \\)
3. \\( T(F_i) < B \\): time duration constraint


## Assumptions

1. An event can be described succinctly by the interactions between a few individuals(Constraint 1)

## Greedy randomized algorithm

1. Sampling users
   1. sampling one user by number of emails
   2. sample his neigebors in the graph
   3. this is one vertex set 
2. Slide a window on the above induced graph.
   1. keep removing the most "outlier" edge in terms of coherence
   2. until the coherence score raises above \\(A\\)
   2. save this subgraph
3. Repeat 1 and 2 for several iterations to collect a pool of candidate events
3. For all the candiates from step 3, keep greedily selecting the candidate that has the largest number of edges while not sharing edges with previously selected candaites unitl \\(K\\) candidates are gathered.
   
