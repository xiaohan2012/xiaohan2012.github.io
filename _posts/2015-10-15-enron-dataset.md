---
layout: post
title: "Enronc Dataset"
date: 2015-10-15 12:57:44
categories: dataset
tags: email-network enron
---


# Different data versions

- [MySQL dump](http://www.ahschulz.de/enron-email-data/): a "repaired" version which supports MySQL 5 instead of MySQL 4.
- [Avroizing the Enron Emails](http://hortonworks.com/blog/the-data-lifecycle-part-one-avroizing-the-enron-emails/): using Pig and Avro and pointer to the MySQL dump
- [UC Berkeley Enron Email Analysis](http://bailando.sims.berkeley.edu/enron_email.html)
- [Enron by Joel Pfeiffer from Purdue](https://www.cs.purdue.edu/homes/jpfeiff/enron.html)


# Papers on Enron

## [The Enron Email Dataset Database Schema and Brief Statistical Report](http://foreverdata.com/1009/Enron_Dataset_Report.pdf)

- Database scheme
- Some simple statistics
  - Number of emails per user
  - Email number across time(in 2011, many more emails than before)
  - More diverse communication(beyond the potitional network) happened in 2001

## [Communication Networks from the Enron Email Corpus “It's Always About the People. Enron is no Different”](http://link.springer.com/article/10.1007/s10588-005-5377-0)

### Enron story

Illegal accounting and business practices hidden by the auditors until it went public.

### Related work

Berry  and  Browne  (2005)  tracked  and  extracted  topics  and  then  clustered  messages to identify critical happenings and individuals

Priebe et al. (2005) used scan statistics to detect outlier data points.

Chapanond et al. (2005) report network analytic  measures on their graph

McCallum et al. (2005) combined social network information extracted from sender-recipient relations with information on the topic of emails that they identified by statistical analysis  of  word  distributions  into  the  ART  model.

### Data

Different from previous approach, the graph is directed and contains 557 users(more than the original 151).

Some enhancement is made:

1. each person is linked to multiple addrs. So they are linking by people(even with different name spellings) instead by email address
2. assigning job rank to users and track their job positions(we can view the network in different scopes, from individuals or from ranks)

May 1999 - March 2002(35 months) are the major months of communication.

Figure 2: three vertical lines(events) and the number of emails across time.

### Analysis/Result

[ORA](http://www.casos.cs.cmu.edu/projects/ora/) is a dynamic network analysis toolkit for assessing and comparing complex network data that changes over time.

- Number of *stong components* reached its peak during the crisis.
- *Disparity*(measured by betweenness and degree centralization) in employee’s communication control raised and reached its peak in December 2001, This indicates a more diverse communication network.
- Number of email instances *received* per rank show similar patterns, while for *sent*, different patterns for different ranks(senioir management sent the most emails)
- Different rank group show different *upward/downward/lateral* communication patterns across time.


The data is not publicly available!

## [Email Surveillance Using Non-negative Matrix Factorization](http://link.springer.com/article/10.1007%2Fs10588-005-5380-5)


Some temporal topic finding(copied from the paper):

The year(2001) began with California Governor Gray Davis calling for an investigation of Enron in light of the 2000 California Energy crisis and it was an ongoing topic throughout the year.
To a lesser degree, the discussion and legal battles involving the Dabhol Power Company were also consistently present throughout 2001.

Some illustration:

![](/assets/images/graph-summary/enron-temporal-topic.png)

# Questions

Corporate email network might be different than the traditional communication network, which is often peer-to-peer.

Some potential issues

- In corporate email network, one email can be sent to multiple recipients(can be hundreds, some notification, for example) and most of the recipients do not reply. When modeling topics of a event/subgraph, one message should be only considered once
- When the communication is one-directional(the recipient did not respond), should we consider it an interaction? Or should it correspond to an edge? Can we ignore notification/broadcast emails(emails with low response rate)?
- News feeds can potentially contaminate the topics

# Expected output characteristics

The Output of the dynamic graph summary algorithm should have the following characteristics:

- Fewer events before the crisis than the crisis period
- Event tend to be related to investiation, bankrupcy during the crisis period
- Event graph tend to be more diverse during the crisis period


# More dataset

Besides the email network, we can analyze the following network:

1. Company-company-news network: we can collect public news data(from [Bloomberg](http://www.bloomberg.com/europe), for example) between different companies across different time periods and discover what happens between them at different temporal position. 
2. Q&A network
