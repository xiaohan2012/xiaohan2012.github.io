---
layout: post
title: "MMDS: Link Analysis and Page Rank"
date: 2015-10-16 14:34:36
categories: course
tags: link-analysis page-rank
---

# Example of real-life graph:

- Social network
- Social media(blogs)
- Citation network between different journals(of different fields)
- Internet: computers and routers
- Technological network: road, water, powergrid, etc
- Web

# Organizing the web

Used to be manual classification of web sites, but it doesn't scale.

Now we opt to information retrieval, which aims to find a set of relevant small set of trustful webpages.

We need trustful webpages because there are many spams and random webpages on the Web.

Problems to tackle:

- Who to trust?
- Find best answer to a query

Several algorithms:

- PageRank
- Hub and Authority
- Topic specific PageRank
- Web Spam Detection

# PageRank

## Initial formuation / Flow formulation

Intuition: a page is important if it's pointed to by important pages(recursive)

Mathmatically:

$$ r_j = \sum\limits_{i \rightarrow j} \frac{r_i}{d_i}$$

where \\(r\\) is the importance of a webpage and \\(d\\) is its out-degree.

Note a page can point to itself.

For \\(N\\) variables and \\(N\\) equations, constraint is required. Otherwise, not unique solution.

Gaussian elimination is one way but does not scale well.
