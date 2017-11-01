---
title: "Mini hackathon: Sparse Local Embeddings for Extreme Multi-label Classification"
desc: "took one day off for fun"
---


# what and why: mini hackathon

mini hackathon is a personal coding event in which:

>>> I spend *one day* to code up the method(s) in a research paper

why am I doing this?

because doing PhD requires me reading a lot of papers, while leaving little time for coding, which is a lot of fun to me.

too much reading and little coding makes me a dull boy (i'm not saying reading is not fun). 

plus, coding up the methods in my favourite papers help me learn better about the methodologies.

# project description

the goal is to implement the method in [Sparse Local Embeddings for Extreme Multi-label Classification, NIPS 2015](https://papers.nips.cc/paper/5969-sparse-local-embeddings-for-extreme-multi-label-classification)

main components in the method:

1. clustering of the data points
2. build kNN graph from the label vectors
3. embeddings learning from the graph: essentially a low-rank matrix approximation problem
4. linear regression on the input features and learned local embeddings

# technique choices

to avoid re-inventing the wheels, I tried to use exising libraries as much as possible

## clustering

I used [sklearn.cluster.KMeans](http://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html). The matlab code of the paper also uses kmeans. 

Neat and handy. 

## building kNN graph

[sklearn.neighbors.kneighbors_graph](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.kneighbors_graph.html) is a handy choice to build such graph.

it's fast and allows parallelization. I learned that similarity search techniques such as *kd-tree* can be used to speed up the process.

one minor detail, the paper uses dot product similarity to build the graph, which is not supported in `sklearn`

so I switched to cosine distance.

## low-rank matrix completion

[implicit](https://github.com/benfred/implicit) is fast library for matrix-factorization (although the library is intended for collaborative filtering, it suits our case.) 

one detail is, the library only implements `l2` norm. however, the paper uses `l1` to enforce sparsity.

so I'm not able to apply `l1` for now.

## linear regression

[sklearn.linear_model.Ridge](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Ridge.html)

As `l1` is not enforced for the embedding, I used Ridge, which only enforces `l2`.

# preliminary result

I tested on *bibtex* dataset and evaluated using precision at k (p@k):

- p@1: 0.5964 (0.6532)
- p@3: 0.3455 (0.3973)
- p@5: 0.2461 (0.2889)

scores in the parenthesis are from the papers

## next steps

i suspect the worse performance is due to the lack of `l1` norm. so one plan is to add `l1`.

# source code

- [sleec](https://github.com/xiaohan2012/sleec_python)

some statistics:

I managed to code up the method using far fewer code than the code from the author

- the paper author wrote **4861** lines of matlab code and **3924** of cpp code
- I wrote **248** lines of python code by using existing software packages

