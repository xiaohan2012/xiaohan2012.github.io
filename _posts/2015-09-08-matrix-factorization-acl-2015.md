---
layout: post
title:  "ACL 2015 tutorial summary: Matrix/Tensor Factorization for NLP"
date:   2015-09-09 14:12:19
categories: tutorial
tags: matrix-factorization knowledge-base-completion relation-learning
---


# Matrix and Tensor Factorization Methods for Natural Language Processing

[Tutorial link](http://acl2015.org/tutorials-t5.html)

## Basics

### Linear algebra

inner/outter product, Hadamard product(element-wise), element-wise p-norm(Frobenius norm = 2-norm)

### Matrix completion via Low-Rank Factorization

**Matrix completion**: recovery of a matrix

Example applications: guessing missing value in survey data, or estimate distance in sensor node network in which each node has limited range of distance sensing

**Low-Rank Factorization**: \\( Y \approx UV^T \\), \\( Y \in \mathcal{R}^{N \times M}, U \in \mathcal{R}^{N \times L}, V \in \mathcal{R}^{M \times L}\\)

Assumption: \\( rank(Y) = L \ll M,N \\). In other words, \\( Y \\) contains **redundancy** and **noise**. We hope to reconstruct \\( Y \\) using less data based on redundancy.

**Why use it?**


### Three ways to factorize

1. **Singular Value Decomposition**
   \\( Y = UDV^T\\), \\( Y \in \mathcal{R}^{N \times M}, U \in \mathcal{R}^{N \times N}, D \in \mathcal{R} ^ {N \times M}, V \in \mathcal{R}^{M \times M}\\), \\( D \\) is diagonal
   We truncate \\( D \\) to achieve low-rank approximation

2. **Stochastic Gradient Descent** and **Alternating Least Squares*: minimize $$ \left\| \mathbf{Y} - \mathbf{U} \mathbf{V^T} \right\|_F^2 $$.
   CGD slower and counter-intuitive to parallelize compared to Alternating Least Squares, fixing one matrix, treat the other as Least Square problem, which is convex and has closed-form solution.
   The following regularization leads to better readability, compactness and retrieval: $$ \left\| \mathbf{Y} - \mathbf{U} \mathbf{V^T} \right\|_F^2 $$
3. 
##

## NMF


## Related papers

### Overview

- Kolda, Tamara G., and Brett W. Bader. "[Tensor decompositions and applications.](http://epubs.siam.org/doi/pdf/10.1137/07070111X)" SIAM review 51.3 (2009): 455-500.


### Injecting prior / domain knowledge

- Chang, [Typed Tensor Decomposition of Knowledge Bases for Relation Extraction](http://research.microsoft.com/apps/pubs/default.aspx?id=226677), ACL, 2014
- Sebastian Riedel, [Relation Extraction with Matrix Factorization and Universal Schemas](https://people.cs.umass.edu/~lmyao/papers/univ-schema-tacl.pdf), Joint Human Language Technology Conference/Annual Meeting of the North American Chapter of the Association for Computational Linguistics (HLT-NAACL '13) 2013 
- Rocktäschel, Tim, Sameer Singh, and Sebastian Riedel. "[Injecting Logical Background Knowledge into Embeddings for Relation Extraction.](http://rockt.github.io/pdf/rocktaschel2015injecting.pdf)" Proceedings of the 2015 Human Language Technology Conference of the North American Chapter of the Association of Computational Linguistics. 2015.


### Tensor factorization on relation learning

An instance of Tucker 2

- Nickel, Maximilian, Volker Tresp, and Hans-Peter Kriegel. "[A three-way model for collective learning on multi-relational data.](http://www.cip.ifi.lmu.de/~nickel/data/paper-icml2011.pdf)" Proceedings of the 28th international conference on machine learning (ICML-11). 2011.
- Nickel, Maximilian, Volker Tresp, and Hans-Peter Kriegel. [Factorizing YAGO: scalable machine learning for linked data](http://www.dbs.ifi.lmu.de/~tresp/papers/p271.pdf), WWW, 2012

### Tensor factorization on semantic compositionaliy

- Van de Cruys, Tim, Thierry Poibeau, and Anna Korhonen. "[A tensor-based factorization model of semantic compositionality.](http://www.aclweb.org/anthology/N13-1134.pdf)" Conference of the North American Chapter of the Association of Computational Linguistics (HTL-NAACL). 2013.
- Maximilian Nickel, Kevin Murphy, Volker Tresp, Evgeniy Gabrilovich,  [A Review of Relational Machine Learning for Knowledge Graphs](http://arxiv.org/pdf/1503.00759v2.pdf), IEEE, 2015 (**relation ship to neural network**)

### Combined matrix / tensor factorization

- Singh, Sameer and Rocktaschel, Tim and Riedel, Sebastian, [Towards Combined Matrix and Tensor Factorization for Universal Schema Relation Extraction](http://rockt.github.io/pdf/singh2015towards.pdf), NAACL Workshop on Vector Space Modeling for NLP (VSM), 2015


### Collective matrix factorization

- Singh, Ajit P., and Geoffrey J. Gordon. "[Relational learning via collective matrix factorization.](http://www.cs.cmu.edu/~ggordon/singh-gordon-kdd-factorization.pdf)" Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining. ACM, 2008.


### Bayesian matrix factorization

- Singh, Ajit P., and Geoffrey Gordon. "[A Bayesian matrix factorization model for relational data.](http://www.cs.cmu.edu/~ggordon/singh-gordon-relational.pdf)" arXiv preprint arXiv:1203.3517 (2012).
- Salakhutdinov, Ruslan, and Andriy Mnih. "[Bayesian probabilistic matrix factorization using Markov chain Monte Carlo.](https://www.cs.toronto.edu/~amnih/papers/bpmf.pdf)" Proceedings of the 25th international conference on Machine learning. ACM, 2008.

### Bayesian collective matrix factorization

- Klami, Arto, Guillaume Bouchard, and Abhishek Tripathi. "[Group-sparse embeddings in collective matrix factorization.](http://arxiv.org/pdf/1312.5921.pdf)" arXiv preprint arXiv:1312.5921 (2013).

Bayesian tensor factorization?
Collective tensor factorization?


### Factorization machine

- Steffen Rendle (2010): [Factorization Machines](http://www.ismll.uni-hildesheim.de/pub/pdfs/Rendle2010FM.pdf), in Proceedings of the 10th IEEE International Conference on Data Mining (ICDM 2010), Sydney, Australia.


### Reduce rank regression

- Alan Julian Izenman, Reduced-rank regression for the multivariate linear model, 1975

### Multi task learning

- Kishan Wimalawarne, [Multitask learning meets tensor factorization: task imputation via convex optimization](http://papers.nips.cc/paper/5628-multitask-learning-meets-tensor-factorization-task-imputation-via-convex-optimization.pdf), NIPS, 2014

### Structured prediction for NLP

- Lei, Tao, et al. "[Low-rank tensors for scoring dependency structures.](http://www.anthology.aclweb.org/P/P14/P14-1130.pdf)" Proceedings of the 52nd Annual Meeting of the Association for Computational Linguistics. Vol. 1. 2014.
- Lei, Tao, et al. "[High-order lowrank tensors for semantic role labeling](https://people.csail.mit.edu/taolei/papers/naacl2015.pdf)." Proceedings of the 2015 Conference of the North American Chapter of the Association for Computational Linguistics–Human Language Technologies (NAACLHLT 2015), Denver, Colorado. 2015.
- Crammer, Koby, et al. "[Online passive-aggressive algorithms.](http://machinelearning.wustl.edu/mlpapers/paper_files/CrammerDKSS06.pdf)" The Journal of Machine Learning Research 7 (2006): 551-585.

### Convexification

- Bouchard, Guillaume, Dawei Yin, and Shengbo Guo. "[Convex collective matrix factorization.](http://www.jmlr.org/proceedings/papers/v31/bouchard13a.pdf)" Proceedings of the Sixteenth International Conference on Artificial Intelligence and Statistics. 2013.
- Ryota Tomioka, Taiji Suzuki, [Statistical Performance of Convex Tensor Decomposition](http://papers.nips.cc/paper/4453-statistical-performance-of-convex-tensor-decomposition.pdf), NIPS,  2011
- Hsu, Daniel, Sham M. Kakade, and Tong Zhang. "[A spectral algorithm for learning hidden Markov models.](http://colt2009.cs.mcgill.ca/papers/011.pdf)" Journal of Computer and System Sciences 78.5 (2012): 1460-1480.

[Useful Google search](https://www.google.com/#q=convex+penalty+sum+of+trace+norms)




