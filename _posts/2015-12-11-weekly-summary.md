---
layout: post
title: "Weekly Summary, Dec, 11, 2015"
date: 2015-12-14 13:26:22
categories: weekly-summary
tags: weekly-summary
---


# Done:

- found that LST is not optimal
- experimented on another baseline
- presentation
- thinking about what is missing
- talked to Eric and Kiran about the dataset


# Todo:

- Clustering is also an alternative(similar to the above)
  - can we apply that?
    - Yes, for only the messages in the events, we cluster them into *K* clusters and compare.
  - evaluation?
    - for dataset without ground truth: [silhouette-coefficient](http://scikit-learn.org/stable/modules/clustering.html#silhouette-coefficient)
	- for dataset with ground truth: quite a lot. But we don't have the ground truth clustering labels
- text summarization
  - multi-document(MD) summarization
    - [MEAD](http://www.summarization.com/mead/)
	- some [API market place](https://market.mashape.com/)
	- [text summarization](http://textsummarization.net/)
	- [autosummarizer](http://autosummarizer.com/index.php)
	- [textanalysis](https://market.mashape.com/textanalysis/text-summarization#): credit required
	- [getting started with text summarization](http://textminingonline.com/getting-started-with-the-automatic-text-summarization-api-on-mashape): with more lnks to open source tools
  - evaluation: human inspection
- Check out graph summarization and text summarization
  - possible baselines
  - evaluation methods for text/graph summarization
  - some inspiration on the dataset
    - phonecall network
	- SMS network
	- cooperation&coauthorship network
	- we are not constrained to text, we want just *vectors*. How about image/video/audio?
	- Some social network dataset repository to check
	  - [SNAP](https://snap.stanford.edu/data/web-flickr.html)
	  - [blog post from kdnuggets](http://www.kdnuggets.com/2014/08/interesting-social-media-datasets.html+)
	  - [G7 - Yahoo! Property and Instant Messenger Data use for a Sample of Users, v.1.0 (4.3 Gb)](http://webscope.sandbox.yahoo.com/catalog.php?datatype=g): is their text content there?
	- the TimeCrunch algorithm can be used to select the K important subgraphs
- Can graph partitioning algorithm be applied here?
  - how is it evaluated?
- Model it as K-MST problem
  - Indirect summarization as we can limit the node coverage parameter to a small integer so that a few messages are selected as the event
- Consider Gienmarko's proposed paper on coping with the semantic drift problem
- Evaluation for the topics
- More interpretable summarization
  - Limit the number of messages in each event to be small?
  - Apply text summarization technique to further reduce the message number
- Another question: why use vectors to represent the text? Not keywords?

Then let's consider performance




[TimeCrunch: dynamic graph summiarzation](https://www.cs.cmu.edu/~neilshah/research/papers/TimeCrunch.KDD.2015.pdf)

Learned:

- List of contributions exactly what we can use
- Notations in a table
- As some future direction: non-continuous but periodic events can be detected.
- interesting graph structures(chain, star, clique, periodic, etc)

[Code](http://www.cs.cmu.edu/~neilshah/code/timecrunch_v0.1.tar.gz)

Input: graph of nodes with timestamps
Output: subgraphs that summarize


[Train of thoughts](http://www.cs.cmu.edu/~dshahaf/fp0590-shahaf.pdf)

Structured summary

Represent event as a path(metro line)

- Coherence: min of the pairwise coherence betwen two consecutive documents in the chain.
- Coverage: weighted sum of the extent that each document feature(topics) is covered(we can optimize this as well in our study).
- Connectivity:

Input: documents
Output: \\(G=(V, E)\\) and the map, where \\(V\\) corresponds to each document. Edges are constructed when listing all candidate chains.

What is useful:

- Def of coherence on chain: can we modify it on tree?(Can we define event as a chain of documents)
  - if we use this coherence, then the optimization problem is changed
- Def of coverage: can be applied directly
  - can be used for evaluation
  - or can be used in evaluation
  - we optimize coverage by maximizing # of uniquely covered nodes
- Can we demonstrate how the events are connected? Instead of a linear display.
  - the paper uses connectivity to connect the chains  
