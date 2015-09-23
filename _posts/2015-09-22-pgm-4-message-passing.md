---
layout: post
title: "PGM course: Message Passing"
date: 2015-09-22 16:20:46
categories: course
tags: pgm inference message-passing
---


Ising model: inference can lead to clique of size \\(n)\\, the row/column number, which can be quite large(e.g, image)(try to eliminate variables in the Ising model)

## From Elimination to Message Passing

Elimination = message passong on *clique trees*, where each clique is the resulting elimination clique from certain step of variable elimination.

One run of variable elimination only answer **one** query. When we have **multiple** queries(say, an online inference system), *messages* maybe reused.

Which to precompute?

## Message passing on tree

Three types of trees:

- undirected
- directed: no v-structure
- poly tree: can have multiple parents(the v-structure)

We talk about undirected and directed tree. Undirected tree is equivalent to directed tree as they make the same conditional independencies.

A unique path between any pair of node ensures the correctness of our algorithm.

Definition of *message*: \\( m_{ji}(x_i) = \sum\limits_{x_j} \phi(x_i)\phi(x_i, x_j) \prod\limits_{k \in N(j)\i)} m_{kj}(x_j) \\)

Interpretatin of message: a belief from \\(x_j\\) to \\(x_i\\)

Implication: node can send its message to one of its neighbor once it has received messages from **all other** neighbors.

Elimination and message passing are equivalent on trees: every elimination of variable can be considered as passing one message.

Complexity of computing node marginals using dynamic programming: \\(2C\\), where \\(C\\) is the complexity of message passong on the tree in one direction. We need two passes(in each direction of the tree).

Belief Propagation algorithm:

1. Evidence
2. Choose the root: determines how messages are passed(unqiue in tree)
3. Collect: recursive procedure that collects messages from neighbors. From leaf to root
4. Distribute: similar to Collect but in the reverse direction. From root to leaf
5. Compute marginal

Sequential and parallel(asynchronous) are both possible

BP on tree is correct/consistent. For tree: only one way of passing message from one node to the other so that the result is consistent.

## Message passong on non-tree

Inconsistency on non-tree, for example, the *Misconception* example. The same query on two **different** message passing ways get two different results.

Basic idea: we can **convert** the non-tree graph into a tree...

Factor Graph: graph with variable node and factor node. This type of graph makes explicit the factorization.

Factor tree: the factor graph in which the distinction between variable nodes and factor nodes are ignored and such graph is an undirected tree

**Q**: general algorithm to perform this conversion?

Once we have a tree, we can enjoy consistency of query result,

Two types of messages during message passing on factor tree:

1. from variable to factor: without marginalization
2. from factor to variable: with marginalization

Poly-trees is treated differently in contrast to undirected/directed tree, we need to convert it to factor tree. (why?)

Disadvantages of factor tree conversion: we will might encounter a large clique(which is the bottleneck of computation).

Example: the \\(X_2, X_3 and X_4\\) clique. When passing messages from \\(X_2\\) and \\(X_3\\) to \\(X_4\\) through their factor, we need to sum over \\(X_2, X_3\\) together.

More extreme example: Ising model.

One possible factor tree version is columns/rows of nodes as factors and being connected in a chain.

BP works:

- tree
- tree-like graphs
- poly-trees

## Max-product algorithm

computing maximum a posterior(MAP)

replace *sum* with *max* and extra bookmark keeping.

## Inference on general GM

Junction tree algorithm: obselete and complicated but illustrates the principles behind exact and approximate inference.


Moralize directed graph -> undirected graph

The following is about why we **triangulate**

Clique tree: a undirected tree of cliques.

Separator factor: a factor between two original factors whose variables are the intersection between those of the two connecting factors.

With separator factor: \\(P(X) = \frac{\prod_C \psi_C(\mathbf{X}_C)}{\prod_S \phi_S(\mathbf{X}_S)}\\), \\(C\\) is the clique and \\(S\\) is the separatro


Local consistency property.

Two update(forward/backward) ensures this. Several methods to update: Hugin update, Shafer-Shenoy update.


Guarantee of consistency: junction / running interaction / triangulation property

When two factors share some node, there should be an edge connecting them.


Triangulation: make it into a chordal graph.

If no triangulation for example the Misconception example, we will disobey the running intersection property, thus inconsistency.

How can we add edges for triangulation without changing the conditional independence assumption?

We can manipulate the factors without changing the conditional independence. For example \\(\phi(X_1, X_2, X_3)\\) can equal to \\(\phi(X_1, X_2) \phi(X_2, X_3)\\). In this way, independence between \\(X_1, X_3\\) is ensured.


Junction tree is not equivalent to the graphical model. It's just a device to specify how message passing is done in the graphical model.

How to triangulate? Different triangulation can produce complexity on inference.

Summary of junction tree:

1. moralize the graph
2. triangulate(NP-hard, but heuristic exists)
3. build clique tree(using maximum-spanning tree algorithm. **why?**)
4. BP

Complexity depends on maximum clique.