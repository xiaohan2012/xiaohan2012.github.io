---
layout: post
title: "Graph summary problem"
date: 2015-10-08 12:24:14
categories: modeling
tags: graph graph-summary dynamic-graph
---


# Dynamic network

## Problem definition

Given email communication records, we have input:

$$ r^{(i)} = (u_s^{(i)}, u_t^{(i)}, \mathbf{L}^{(i)}, t^{(i)}), i=1 \ldots M $$

where we have \\(M\\) communication records in total. For the \\(r^{(i)}\\)th record:

- \\(u_s^{(i)}\\): sender
- \\(u_t^{(i)}\\): receiver
- \\( \mathbf{L}^{(i)} = \{l_1^{(i)} \ldots l_{\vert \mathbf{L}^{(i)} \vert}^{(i)}\}\\): a set of labels associated with the email content
- \\(t^{(i)}\\): time the email was sent

For the output, a summary of the reocrds should be the top \\(K\\) *main events* that best capture what happened  in the network. The \\(i\\)th event is defined by:

$$ e^{(i)} = (\mathbf{U}^{(i)}, \mathbf{L}_e^{(i)}, t_1^{(i)}, t_2^{(i)}), i=1 \ldots K $$

where:

1. \\(\mathbf{U}^{(i)}\\): the people/users that are involved in the event,
2. \\(\mathbf{L}_e^{(i)}\\): the labels that best describe the event
3. \\(t_1^{(i)}, t_2^{(i)}\\): the time interval in which the event happened

Some design issues:

- Besides the time interval, should be also include the **communication strength** at different time points?
- Should we include all the labels that appear in the event-speicific records? Probably not, especially for event involing a lot of emails. Then what is the selection criteria?
- Same question for the users. Again, it's desirable to select only the important users involved in the event. And the same question. How to select the important ones?


Some desired properties of the events:

- **coverage**: they should cover as much as the whole communication network. Note that some email records, users and labels are not included in any of the events as they are relatively trivial
- **dense**: emails that happened within some organization/department/group tend to be in the same event
- **time continuity**: emails that happened within relatively short time span tend to be in the same event
- **topical consistency**: emails that describe similar topic tend to be in the same event

## Clustering approach

### Hard clustering(K-kmeans)

The similarity function should consider time, user closeness(in graph), topic relatedness

Potential issue: some trivial/singleton/outlier record(For example, Joe sent me a linl, that's it) should not be included into any of the event. Is there such a clustering algorithm that handles that?

Some graphical illustration:

![](/assets/images/graph-summary/hard-clustering-illustration.png)


### Soft clustering(mixture model)

Instead of using hard clustering, we can switch to soft clustering, for example, using mixture model(EM algorithm).

We can model it as a generative process, where we first select the event, then generate the time/topic/user conditioned on the event.

![](/images/graphviz/gen-b570b3235b7f99222039aa0390a49796.gv.png)

where

- \\(T\\): sending time, multinomial distribution
- \\(L\\): label, multinomial distribution
- \\(U\\): user, multinomial distribution

Potential benefit of mixture model:

- Hard clustering result can be obtained using MAP(selecting the most likely cluster assignment)
- For the user/label selection critia, we can set a threshold as probability is assigned to the generating process from event to time/topic/user.
- Once being Bayesian, we can add our prior as well.

## Dense subgraph detection approach

Find a list of \\(K)\\ subgrpahs that:

1. cover the entire graph as much as possible
2. 

# Dynamic ego network

# Static network

- Can we leverage the label information(possibly on vertice and edge) to perform better community detection?
- 
