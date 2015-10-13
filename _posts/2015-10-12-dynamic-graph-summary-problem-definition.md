---
layout: post
title: "Labeled Dynamic Graph Summary: Problem Definition"
date: 2015-10-12 14:44:00
categories: crafting
tags: graph-summary
---


# General Notation

A *labeled dynamic graph* is defined as \\(G = (V, E) \\), where \\(V\\) is a set of \\(n\\) nodes and \\(E\\) is a set of \\(m\\) time-stamped and multiply-labeled interactions between pairs of nodes.

$$ E = {(u_i, v_i, L_i, t_i)} $$

with $$ i = 1 \ldots m $$ such that \\(u_i, v_i \in V \\), \\(L_i \subseteq \mathcal{L} \\) and \\(t_i \in \mathbb{R} \\), where \\(\mathcal{L}\\) is the global label set.

# Problem Definition 1

An *event* \\(e\\) is defined to be a list of vertices, a label and a time interval, \\( (W, l, [s, t]) \\).

An event \\(e=(W, l, [t_1, t_2])\\) *covers* an edge \\(r=(u, v, L, t) \in E \\) if \\(l \in L \\),  \\(u, v \in W\\) and \\(t_1 \le t \le t_2 \\).

Define the *event size* \\( \vert e \vert = \vert \\{  r \in E \vert e \text{ covers } r \\} \vert \\)

**General problem**:

Given a labeled dynamic graph \\(G = (V, E) \\), a budget \\(A\\) on the maximum time span for each event and a set of other hyperparameters, \\( \eta \\), find \\(K\\) events

$$ e_i = (W_i, l_i, [t_{i,1}, t_{i,2}]), i=1 \ldots K $$

that maximizes

$$ \sum\limits_{i=1 \ldots K} q(e_i) $$

, where \\(q(e_i)  \\) is some *quality function*.

under the constraint

$$ t_{i,2} - t_{i,1} \le A \text{ and } \vert W_i \vert \le B $$

and a set of other problem-specific constraints \\(\mathcal{C}\\)


# Variants

Under the above general definition, it's flexible to define different variants with different designing concerns.


## Maximizing interaction coverage

Our goal can be: cover as many interactions in \\(G = (V, E) \\) as possible. 

An event \\(e=(W, l, [t_1, t_2])\\) *covers* an edge \\(r=(u, v, L, t) \in E \\) if \\(l \in L \\),  \\(u, v \in W\\) and \\(t_1 \le t \le t_2 \\).

Define the *event size* \\( \vert e \vert = \vert \\{  r \in E \vert e \text{ covers } r \\} \vert \\)

This goal can be translated into:

$$ q(e) = \vert e \vert $$

with another contraint

$$ \vert W_i \vert \le B, i = 1 \ldots K $$

where \\(B\\) is another budget parameter.

Some property of this design: the induced subgraph of event \\(e\\) might not be dense.

For example, several communities are talking about the same thing at the same time point. Is this design good or not?

If we only care about the label, it's not a problem. If we'd like to know the participants in the graph, it would be good to separate the event across communities.


## Maximizing density

The above design does not capture dense subgraphs. In some cases, we might prefer dense interaction graphs. We can define:

$$ q(e=(W, l, [t_1, t_2])) = \frac{2 \vert e \vert}{\vert W \vert} $$

## Find dense-yet-relatively-large subgraphs

Maximizing density might be a bad choice under the following scenario:

During time \\( (t_1, t_2) \\), *A* discussed wtih *B* about "hangout", *B* then passed the message to *C* and discussed with *C* about "hangout" as well. Later, they hang out.

Intuitively, we should have an event, \\(({A, B, C}, "hangout", [t_1, t_2]) \\)

However, this result does not have the highest density. Both \\(({A, B}, "hangout", [t_1, t_2]) \\) and \\(({B, C}, "hangout", [t_1, t_2]) \\) have higher density.

To address this issue, we would like to have the quality function also take into account the subgraph size. Something like:

$$ q(e=(W, l, [t_1, t_2])) = \frac{2 \vert e \vert}{\vert W \vert} \log \vert W \vert $$

Or weighted sum of the two quantities:

$$ q(e=(W, l, [t_1, t_2])) = \alpha \frac{2 \vert e \vert}{\vert W \vert} + \beta \log \vert W \vert $$

where \\( \alpha, \beta \\) are hyperparameters.

## Label purity/coherence

Ideally, we would like the interactions within an event to be about the similar topic. In case the event label is very general(like "paper" or "meeting"), it's very likely the event is not really an event. A series of very different "meeting" interaction might happen within the same period of time or discussion on several different papers could happen as well.

Define \\( X_e \\) as the event of seeing some label in event \\(e\\), which follows some multinomial distribution.

Then the *coherence* of an event \\(e\\) is \\(H(X_e)\\), where \\(H\\) is the entropy function.

The more topically coherent, the lower \\(H(X_e)\\).

We can add some topic coherence constraint:

$$ H(X_e)  \le C $$, where \\(C\\) is some maximum threshold.

# Problem with single label requirement



----------------------


**Proposal**: Is it possible that we contrain all edges in some event to share at least one label?

Is it possible that edges that are in some logical event do not share label? For example, one set of edges are about "hangout" without "picnic" and another set edges are about "picnic" without "hangout", but they are talking about the same thing? 

Under the above proposal, the keyword quality matters(ideally, we would like both edge set to share some label) or as an alternative, we allow multiple labels for each event. 

Problems with keyword: hard to extract keywords for some social network interactions. For example, Facebook chat. Because text in some interactions are too short(such as "Thanks", "ok") for the keyword extractor to extract anything meaningful. To address this issue, one might merge the tiny interactions into a bigger and more meaniningful one(merging information within one day as a bigger one?)




What are the other dataset that we can use?

- Transaction network: \\(A\\) and \\(B\\) traded some good \\(X\\) at time \\(t\\)
