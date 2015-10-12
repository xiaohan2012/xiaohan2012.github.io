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

For \\(G = (V, E) \\)  and a subset of edges \\(D \subseteq E\\), the *induced graph* of \\(D\\) is defined as \\(G(D) = (V(D), D)\\) such that \\(V(D)\\) consists of all the nodes that occurs in \\(D\\).

For a subset of edges \\(D \subseteq E\\), we define the *time span*  \\( T(D) = max_t {t \vert (u, v, l, t) \in D}  - min_i {t \vert (u, v, l, t) \in D}\\)

For a label \\(l\\) and an edge set \\(D\\), we define the *label coverage ratio* \\(r(l, D) = \frac{N(l, D)}{\vert D \vert}\\) where \\(N(l, D) = \vert {(u, v, L, t) \in D \vert l \in L} \vert \\).



# Problem Definition

Given a labeled dynamic graph \\(G = (V, E) \\), a budget \\(A\\) on the total time span and minimum label coverage ratio threshold, \\(B\\) , find the set of edges \\(D \subseteq E\\) and a label \\(l \in \mathcal{L}\\) that maximizes some quality function \\(q(D, l, G)\\) such that \\(T(D) \le A\\) and \\(r(l, D) \ge B\\).

