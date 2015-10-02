---
layout: post
title: "Useful paper on email mining"
date: 2015-10-01 16:25:46
categories: resource
tags: thesis graph-mining topic-mining
---

## Network structure

### Only user

Edge between A and B can be defined as A sent an email to B. Edge weight can be the number of emails A sent to B.

### Both user and email

We can add email node as the bridge between user-user interaction. For example, two edges from user U1 to email E and from E to user U2 can be added if U1 sent E to U2.

### More information

Various types of nodes/edges maybe added to enrich the network:

1. Two emails might be connected by their heading in title. For example: the "[Hwfat2]" in some title "[Hwfat2] HWFA meeting ...".
2. Two user might be connected if they are in the same group
3. Two emails might share some important keywords, for example, "Deadline for KDD" and such keywords can be a different type of node

## Community labeling(both static and dynamic)

Simplest idea on **static** graph:

1. Detect the community
2. Get BoW representation for each community, so we have a matrix(row: community, column: terms)
3. Selecting the important terms by either:
   - reweighing the matrix using TdIdf
   - topic mining on the entire corpus(possibly adding some network structure constraint)

How to extend to **dynamic** graph? What do we want?

Following is one related paper:

- Large-Scale Community Detection on YouTube for Topic Discovery and Exploration

## Topic/event/anomaly detection

- *Emerging Topic Detection on Twitter based on Temporal and Social Terms Evaluation*: only terms are extracted. We can extract summaries/phrases
- ePeriodicity: Mining Event Periodicity from Incomplete Observations
- [ICDM 2014 Tutorial Node and graph similarity: Theory and Applications](http://web.eecs.umich.edu/~dkoutra/tut/icdm14.html#outline)
- [Query-Based Outlier Detection in Heterogeneous Information Networks](http://hanj.cs.illinois.edu/pdf/edbt15_jkuck.pdf)
- [Outlier Detection for Temporal Data: A Survey(Section 6)](http://research.microsoft.com/pubs/217054/gupta14_tkde.pdf)
- [Anomaly Detection in Dynamic Networks: A Survey](http://cs.ucsb.edu/~victor/pub/ucsb/mae/references/ranshous-anomaly-detection-in-networks-survey-2014.pdf)
- [Mining Query-Based Subnetwork Outliers in Heterogeneous Information Networks](http://hanj.cs.illinois.edu/pdf/icdm14_hzhuang.pdf)
- [Community Change Detection in Dynamic Networks in Noisy Environment](http://www.www2015.it/documents/proceedings/companion/p793.pdf)
- Local Learning for Mining Outlier Subgraphs from Network Datasets
- [Event detection in dynamic graphs](http://www3.cs.stonybrook.edu/~leman/wsdm13/WSDM13-Tutorial%20-%20PartII.pdf)

## Role detection

- [Entity Role Discovery in Hierarchical Topical Communities](http://hanj.cs.illinois.edu/pdf/mds13_mdanilevsky.pdf)

## Topic evoluion

- [TextFlow: Towards Better Understanding of Evolving Topics in Text](http://research.microsoft.com/en-us/um/people/weiweicu/images/flow.pdf)
  1, Topic merging/splitting; 2, visualizaiton
- [Detecting topic evolution in scientific literature: how can citations help?](http://dl.acm.org/citation.cfm?id=1646076)
- [Topic evolution and social interactions: how authors effect research](http://dl.acm.org/citation.cfm?id=1183653)


## Keyword extraction for document / topic mining

- *Extracting Key Terms From Noisy and Multi-theme Documents*: one graph per document
  Can we take into account more documents in the graph so that keywords is not soled defined by the document itself but also other documents?
- Automatic Construction and Ranking of Topical Keyphrases on Collections of Short Documents


## Community detection in general

- [Mining hidden community in heterogeneous social networks](http://dl.acm.org/citation.cfm?id=1134280): from single-network, user-independent analysis to multi-network, user-dependant, and query-based analysis
- *Combining link and content for community detection*: a discriminative approach: probabilistic approach
- Detecting Overlapping Communities from Local Spectral Subspaces
- [Recipient suggestion for electronic messages using local social network data](http://ceur-ws.org/Vol-1308/paper6.pdf)

### Nested community detection

- [Uncovering Hierarchical and Overlapping Communities with a Local-First Approach](http://dl.acm.org/citation.cfm?id=2629511)

## Interesting papers from ICDM

### Mining social network

- From Micro to Macro: Uncovering and Predicting Information Cascading Process with Behavioral Dynamics
- Mining Multi-Aspect Reflection of News Events in Twitter: Discovery, Linking and Presentation(paper not available)
- Suhas Ranganath, Suhang Wang, Xia Hu, Jiliang Tang and Huan Liu [Finding Time-Critical Responses for Information Seeking in Social Media](http://www.public.asu.edu/~swang187/publications/ICDM_2015.pdf), ICDM 2015
  Problem: rank responders for a question to provide timely and relevant answer
  Seems that no network structural information is used
- Scott Deeann Chen, Ying-Yu Chen, Jiawei Han, and Pierre Moulin [A Feature-Enhanced Ranking-Based Classifier for Multimodal Data and Heterogeneous Information Networks](http://hanj.cs.illinois.edu/pdf/icdm13_schen.pdf), ICDM 2013
  Rank and classify at the same time on heterogenous network by propagating class information via edges
  Very interesting ideas in general

# Spatio-temporal mining

- Jingrui He, Yan Liu andRichard Lawrence [Rare Category Detection on Time-Evolving Graphs](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.188.6366&rep=rep1&type=pdf), ICDM 2008
  Problem: identify examples of rare classes in unlabeled dataset(fraud detection, network intrusion detection)
  Extension: detect spam in forum/email network
  

### Network mining

- Learning Predictive Substructures with Regularization for Network Data
- Misael Mongiovi, [Mining Evolving Network Processes](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=6729538&tag=1), ICDM 2013
  Problem: mining smoothly evolving process(e.g, traffic jam, information foragin). Given a struturally static graph and time-varying edge weights, find snapshots of subgraphs that are smoothly evolving
  

### Text mining

- Chi Wang, Marina Danilevsky, Jialu Liu, Nihit Desai, Heng Ji, Jiawei Han [Constructing Topical Hierarchies in Heterogeneous Information Networks](http://nlp.cs.rpi.edu/paper/icdm13.pdf), ICDM 2013
  Hierarchical topic modeling using link information in heterogeneous network
- Incorporating World Knowledge to Document Clustering via Heterogeneous Information Networks
- Topic Periodicity Discovery from Text Data


## Random stuff

- [Mining Collective Intelligence in Diverse Groups](http://hanj.cs.illinois.edu/pdf/www13_gqi.pdf)
- [Unsupervised Link Selection in Networks](http://hanj.cs.illinois.edu/pdf/aistat13_qgu2.pdf)


## Tutorials

- [ICDM 2015](http://icdm2015.stonybrook.edu/program/schedules)

# Interesing topics:


## Topic evolution

[TextFlow: Towards Better Understanding of Evolving Topics in Text](http://research.microsoft.com/en-us/um/people/weiweicu/images/flow.pdf):

1. captures the merging and splitting of topics(the image in the paper is quite cool):
2. captures important events
3. cool visualization

Extensions:

- Can we incorporate network structure to contrain the topics, just like [Detecting topic evolution in scientific literature: how can citations help?](http://dl.acm.org/citation.cfm?id=1646076)
- Can we capture the evolving topics for user/community/group?

[Topic evolution and social interactions: how authors effect research](http://dl.acm.org/citation.cfm?id=1183653):

1. models how topics are transitioned though Markov transition matrix

Maybe the "topic transition" can be applied.

## Outlier/anomaly detection

What does it mean by outlier/anomaly for email applications?

- A new contact that haven't contacted you before
- Community outlier(some guy from a totally different area in terms of a specific research group)
- Spam
- Totally unrelated email to the current topic?
- Some specific structure? Like a event(paper deadline, party discussion)?

See the anomaly categorization in [Anomaly Detection in Dynamic Networks: A Survey](http://cs.ucsb.edu/~victor/pub/ucsb/mae/references/ranshous-anomaly-detection-in-networks-survey-2014.pdf)



## Others

- Labeling relationship between two email users. For example, the relationship between A and B can be captured by "machine learning" and "thesis"
- Email grouping: can we group the incoming/unread emails so that similar ones(course registraion by student) are together and the user can process them all together?




