---
layout: post
title: "Labeled Dynamic Graph Summary: Problem Definition"
date: 2015-10-12 14:44:00
categories: crafting
tags: graph-summary
---

# Notation

A *labeled dynamic graph* is defined as \\(G = (V, E) \\), where \\(V\\) is a set of \\(n\\) nodes and \\(E\\) is a set of \\(m\\) time-stamped and multiply-labeled interactions between pairs of nodes.

$$ E = {(u_i, v_i, L_i, t_i)} $$

with $$ i = 1 \ldots m $$ such that \\(u_i, v_i \in V \\), \\(L_i \subseteq \mathcal{L} \\) and \\(t_i \in \mathbb{R} \\), where \\(\mathcal{L}\\) is the global label set.

For \\(G = (V, E) \\)  and a subset of edges \\(D \subseteq E\\), the *induced graph* of \\(D\\) is defined as \\(G(D, G) = (V(D, G), D)\\) such that \\(V(D, G)\\) consists of all the nodes that occurs in \\(D\\).

For a subset of edges \\(D \subseteq E\\), we define the *time span*  as

$$ T(D) = max_t ( \\{t \vert (u, v, L, t) \in D\\} )  - min_i ( \\{t \vert (u, v, L, t) \in D\\} ) $$

For a label \\(l\\) and an edge set \\(D\\), we define the *label coverage ratio* \\(r(l, D) = \frac{N(l, D)}{\vert D \vert}\\) where \\(N(l, D) = \vert \\{ (u, v, L, t) \in D \vert l \in L\\} \vert \\).

# Problem Definition

Given a labeled dynamic graph \\(G = (V, E) \\), a budget \\(A\\) on the total time span and minimum label coverage ratio threshold, \\(B\\), find the set of edges \\(D \subseteq E\\) and a label \\(l \in \mathcal{L}\\) that maximizes some quality function \\(q(D, G)\\) such that \\(T(D) \le A\\) and \\(r(l, D) \ge B\\).

For example, the quality function corresponds to the density of the induced graph, \\(q(D, G) = \frac{2 \vert D \vert}{\vert V(D, G) \vert}\\).

Then, finding the top-k event reduces to sequentially finding K best edge set for the graph where edges, which are in previously-found event, are removed from the graph.

# Example & question

![](/images/graphviz/email-network-ambiguous-partition-example.dot.png)

Consider the example, A, B and C talked about {"hangout", "nuuksio"} at some point. We have the edges, \\(E_1\\):

- \\( (A, B, \\{"hangout", "nuuksio"\\}, 2) \\)
- \\( (A, C, \\{"hangout"\\}, 1) \\)
- \\( (B, C, \\{"hangout"\\}, 1) \\)

At the same time, C told D and E about Y and they chatted. The edges are \\(E_2\\):

- \\( (C, D, \\{"hangout"\\}, 2) \\)
- \\( (C, E, \\{"hangout"\\}, 2) \\)
- \\( (D, E, \\{"hangout", "picnic"\\}, 2) \\)

And later, A, B, C, D and E hangout to Nuuksio National Park.

Intuitively, we might consider \\((E_1 \cap E_2, \\{"hangout"\\}) \\) as one event. However, we cannot get this under the above problem definition. Given \\(A=2\\) and \\(B=0.8\\), we have\\((E_1, X)\\) and \\((E_e, Y)\\) achieves better score/density while satisfying the constraint. 

How to make the problem definition favor \\((E_1 \cap E_2, \\{X, Y\\}) \\) as one event?

The above implies that densely connected induced subgraph is not necessarily desirable, especially when it's small. One possible approach is to encourage event with moderately larger graph size. For example, the quality function becomes:

$$ q(D, G) = \frac{2 \vert D \vert \log \vert V(D, G) \vert}{\vert V(D, G) \vert} $$


Is the label coverage ratio reasonable? In the above example, it's 1.0. What if it's 0.9, can it be the solution?
