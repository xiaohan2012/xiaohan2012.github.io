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

For the output, a summary of the reocrds should be the top \\(K\\) *main events* that best capture what happened  in the network. The \\(i\\) event is defined by:

$$ e^{(i)} = (\mathbf{U}^{(i)}, \mathbf{L}_e^{(i)}, t_1^{(i)}, t_2^{(i)}), i=1 \ldots K $$

where:

1. \\(\mathbf{U}^{(i)}\\): the people/users that are involved in the event,
2. \\(\mathbf{L}_e^{(i)}\\): the labels that best describe the event
3. \\(t_1^{(i)}, t_2^{(i)}\\): the time interval in which the event happened


## Model



# Dynamic ego network

# Static network
