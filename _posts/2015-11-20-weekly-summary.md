---
layout: post
title: "Weekly Summary, Nov, 20, 2015"
date: 2015-11-20 16:10:25
categories: weekly-summary
tags: weekly-summary
---



# Done

- Wrote documentation
- Read "something" on primal dual approximation
- Dicussion
  - Converting general DAG to binary DAG(maybe not that complicated)
  - Dealing with multiple receivers(create separate nodes)


# Todo

- Fix the documentation
- Experiment
  - Better preprocessing of emails(if necessary)
  - Topic modeling(which tools to use? Is the online LDA the same as original LDA?)
  - Find one best event for both models
  - Find K best event
- Think about other datasets
- More reading on Primal Dual approximation(if time available)

  
# Note on primal dual approximation

General method to design and analyzie optimality for approximation method for NP-hard problems.

Dijkstra's s-t shortest path algorithm, Kruskal's minimum spanning tree and Edmond's algorithm for minimum cost branching algorithm can all be intrepretted under and analyzed using this method.

Important concepts and what it can be used for

- Primal/dual complementary slackness: 1) if both are fulfilled by some feasible solution, we get the optimal solution. 2) transform the original problem into a series of simpler primal dual problems(without the weighting coefficients) and improve the dual answer step by step 3) once we know some solution, we can check if it's optimal by solving the dual and check the objective value is equal
- Minimum perfect matching problem in bipartite graph: just another problem, applications such as matching making, player matching

- hitting set problem: given a list of sets \\(\mathcal{T}\\) from some grounding set, choose a subset with minimum weight from the grounding set such that it covers each set in \\(\mathcal{T}\\). Dijkstra', Edmond's ad Kruskal's algorithm can be reduced to this problem.

Very complicated. Gave up!




