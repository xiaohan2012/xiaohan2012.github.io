---
layout: post
title: "Entity Linking Literature Review"
date: 2015-11-02 17:25:25
categories: review
tags: entity-linking
---

# Resources

[WSDM 2014](https://www.dropbox.com/sh/ekzqc3mdk4qkdlw/Egdq-WfUa7#)

- Entity Linking
- Entity Retrieval

[Wikification and Beyond](http://nlp.cs.rpi.edu/paper/wikificationtutorial.pdf)
[Reading list by Heng Ji](http://nlp.cs.rpi.edu/kbp/2014/elreading.html)


# [Milne 2008, Learning to Link with Wikipedia](http://www.cs.waikato.ac.nz/~ihw/papers/08-DNM-IHW-LearningToLinkWithWikipedia.pdf)

Steps:

1. Find a set of candidate terms and DA(disambiguation) on them
2. Using features dervied from 1, LD(link detection) on those disambiguated terms to decide whether the term should be linked or not.

Key idea: DA informs LD.

Can be effectively for lone text but not necessarily short ones, in which context information becomes less, thus unreliable.

## DA

A binary classifier with three features, whose input is a term and a Wikipedia article and output the probability that the term should be disambiguated as such. Three features are used:

  - commonness(popularity): probability that term \\(t\\) linked to sense \\(s\\)
  - weighted average of relatedness(to its context(unambiguous) terms)
  - quality of context: sum of context terms' weight

Training data: links in Wikipedia articles as positive.

More details:

- Context term's relatedness: \\( r(t, s) = \frac{1}{\vert C \vert-1} \sum\limits_{s^{'} \in C \\ s} rel(s, s^{'}) \\), where \\(t\\) is the term, \\(s\\) is the sense and \\(C\\) is the context terms.
- Context term's link probability \\(lp(t)\\): probability that \\(t\\) is linked to some sense

Context term's weight \\(w(t, s) = (r(t, s) + lp(t)) / 2\\)

The second feature \\(f(t, s) = \frac{1}{Z} \sum\limits_{(s^{'}, t^{'}) \in C} rel(s, s^{'}) w(t^{'}, s^{'})\\), where \\(Z\\) is \\(\sum w(t^{'}, s^{'})\\).

## LD

A binary classifier is used to predict the proba that a disambiguated term should be linked/selected.

Use Wikipedia articles to train the classifier. Positive(negative) examples: terms that are(aren't) linked.

Features include: max/mean link probability, max/mean of relatedness, disambiguation confidence(result from DA), generality(article depth in the Wikipedia concept tree), position and spread.

## Comments

- This method may not suitable for short text as it relies on unambiguous terms to determine some feature values. 

# [Hoffart 2011, Robust Disambiguation of Named Entities in Text](http://aclweb.org/anthology/D/D11/D11-1072.pdf)

## Objective

$$ argmax_{e_{1 \ldots k}} \alpha \sum\limits_{i=1\ldots k} prior(m_i, e_i) + \beta \sum\limits_{i=1\ldots k} sim(cxt(m_i), cxt(e_i)) + \gamma coh(e_1 \ldots e_k) $$

s.t.

$$ \alpha + \beta + \gamma = 1$$

$$ e_i \in cnd(m_i) $$

- Use Stanford NER to detect noun phrases
- Prior probability for entity: "Kashmir" refers to Kashmir (the region) in 90.91% of all occurrences.
- Similarity between mention and entity. **Keyphrases** of entity as the bridge between entity and mention.
  - keyphrases of entity, \\( KP(e) \\): anchor text that links to it and titles of articles that link to it.
  - each word in keyphrase has a score/weight with the entity, can be PMI or IDF
  - for each keyphrase, \\(q\\)(which partially matches the context), a score, \\(score(q)\\) is calculated based on the "cover" of the keyphrase in the mention context. Definition in paper. How the cover is computed(shortest window of words that contains a maximal number of words of the keyphrase)?
  - \\( simscore(m, e) = \sum\limits_{p \in KP(e)} score(q)\\)
  - syntax-based similarity: more in paper(Thater10)
- Coherence score among a set of entities: the coherence score between a pair of entities uses "relatedness" in (Milne08)

## Graph problem

The above optimization problem can be converted to a graph problem. The graph consists:

- two types of nodes: mention and entity weighted by similarity between mention and entity or *optionally* combined with the popularity prior.
- two types of edges: mention-entity and entity-entity

Dense subgraph detection(**Wow!**): find the *dense* subgraph that covers all mention nodes and for each mention node, there is an edge to one entity.

*density* definition: minimum of weighted degree among all its nodes(total weight of all incident edges of a node). To cope the long-tail problem(less prominent and sparsely-connected entities)

NP-hard prblem. Approximate algorithm:

- prune entites that are remotely related to mentions(resulting in a smaller graph)
- iterately remove the edge that has the least weighted degree without violating the structure constraint. Track the best subgraph
- return the best subgraph


## Robustness tests

Cope with short text and thematically heterogeneous.

- **prior test**: For short text, the result can be dominated by a few false alternatives with relatedly high prior(say, <= 90%). In that case, we ignore it. This is checked for each mention. **Key idea**: for short text, context might matter more than prior(fixed).
- **coherence test**: for each mention, if the disagreement between prior and similarity is big, we use coherence to solve the conflict. Otherwise, we'd better ignore it because if the text is thematically heterogeneous, coherence score(low) will damage.

Very clever.

## Comments

1. How this methods deal with short text?

# [Ratinov 2011, Local and Global Algorithms for Disambiguation to Wikipedia](http://web.eecs.umich.edu/~mrander/pubs/RatinovDoRo.pdf)

Prons and cons of local and global approach.

## Objective:

$$ \Gamma^{*} \approx argmax_{\Gamma} \sum\limits_{i=1}^N [ \phi(m_i, t_i) + \sum\limits_{t_j \in \Gamma^{'}} \psi(t_i, t_j)] $$

where \\(\Gamma^{'}\\) is the disambiguation context, an approximation to \\( \Gamma^{*} \\)

$$ \phi(m, t)  = \sum\limits_i w_i \phi_i(m, t)$$

where \\(\phi_i(m, t)\\) is a feature and \\(w_i\\) a weight to be learned from bootstrap data(how?)

## GLOW algorithm

Give document and mentions(in contrast to Haffort 2011, where mentions are detected)

1. Augment with other linkable mentions
2. Candidate generation for the augmented mention set
3. Ranker: find the best disambiguated entity for each mention
4. Linker: map some mention-entity pair to *null* to improve the objective function value.

### Mention augmentation + candidate generation

NER+chunking noun phrases with its substrings. If appears as Wikipedia anchor texts, then it's a mention.

For each mention, top-K entities(from Wikipedia data) is used as candidate. \\(P(t \vert m)\\) and \\(P(t)\\) as local features.

### Ranker

Disambiguation context(a set of mapped entities that we are confident about
): use some classifier to decide. Essentially, strike a balance between aggressive(taking all NEs) and conservative(taking all unambiguous).

Features: **max/avg** of NGD(Milne 2008) and PMI on inlink/**outlink**  to \\( \Gamma^{'} \\)

### Linker

Include all Ranker features, plus:

- confidence of second best entity \\(t^{'}\\)
- percentage that \\(m\\) is linked in Wikipedia
- entropy of \\(P(t \vert m)\\): **why?**

### Learning feature weights

Like Milne 2008, use Wikipedia links as positive training examples and those candidates not matching the mention as negative ones.

Ranker and Linker are trained separately.

## Comment

- Compared to Haffort 2011, this approach is relatively local as we select the entity for each mention independently. However, it uses richer set of features and tuned the feature weights by learning from Wikipedia. **Is it possible to combine the global inference from Haffort 2011 and richer features from here?**
- Why Linker step improves the objective function value?
- Similar to Milne 2008, it follows the LG-DA-MD order. The improvement is incorporating the entity-entity relationships and more features.


# [TAGME: On-the-fly Annotation of Short Text Fragments(by Wikipedia Entities)](http://www.di.unipi.it/~ferragin/cikm2010.pdf)

Speed is a big concern.

## Mention detection

Mentions are detected by matching the text to Wikipedia anchors extracted apriori. This decision might be concerned with speed.

## DA
Detect all linkable anchors(mentions), \\(\mathcal{A}\\), for each anchor, \\(a\\) and a candidate sense, \\(p_a\\), define the vote from another anchor \\(b\\) as:

$$ vote_b(p_a) = \frac{\sum\limits_{p_b \in Pb(b)} rel(p_b, p_a) P( p_b \vert b)}{\vert Pb(b) \vert} $$

where \\(P( p_b \vert b)\\) is *commonness*.

Note when \\(b\\) is unambiguous, \\(vote_b(p_a) = rel(p_b, p_a) \\). In contrast to Milne 2008, **all** mentions are used. Might be the reason it's good for short text.

*Goodness* of \\(a \rightarrow p_a \\) is \\( rel_a(p_a) = \sum\limits_{b \in \mathcal{A}} vote_b(p_a)\\).

Each mention is DA independently.

And two methods:

- DA by classifier: a classifier using two features \\(rel_a(p_a)\\) and \\(P(p_a \vert a)\\) and pick the one with the highest probability
- DA by threshold: obtain the top \\(k\\) senses ranked by \\(rel_a(p_a)\\) and select the one with the highest commoness, \\(P(p_a \vert a)\\)

For speed, low commonness(below a specified threshold) senses are removed.

## LD

Two features for anchor \\(a\\):

- link probability \\(lp(a)\\)
- cohernece(average relatedness) \\( \frac{1}{\vert \mathcal{A} \vert - 1} \sum\limits_{b \in \mathcal{A} \\ a} rel(p_a, p_b) \\)

Two ways to combine and get a score:

- average
- linear regression

One we have the score, use some threshold \\(\rho_{NA}\\). If below the threshold, it's *NA*.

## Comments

- Compared to Milne 2008, it uses all anchors as the context. Meanwhile, it uses fewer features for the decision problems.

# [A Framework for Benchmarking Entity-Annotation Systems](http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/40749.pdf)

## Coping with different scopes/problems in differnt papers

Different tools for different problems(see the list of problems in original paper). And different datasets are for different problems. We need to a way to uniformly evaluate those tools.

Certain problems(easier ones) *reduces* to other problems(harder ones). For example, *Annotation to Wikipedia* reduces *Scored Annotation to Wikipedia(Sa2W)*. This means the solution for the easier problem can be inferred from that for the harder problem efficiently. This means the dataset for one problem can be used for other problems that it reduces to or is reduced from.

Five tools coping the hardest problem(*Sa2W*) are chosen.

## Evaluation measures

### Two side measures

Annotation = mention + entity. One anotation is correct if both parts are correct.

However, challenges:

- Semantic: similar pages/entities(*Barrack Obama* and *President of United States of America*), which can all be potentially correct. This makes the definition of a correct match difficult.
- Syntactic: mention string, "President Barrack Obama" and "Barrack Obama" probably mean the same thing.

We define a binary function \\(M(x^{'}, x)\\) on two Wikipedia articles that determines whether they are "correct match" or not. It may not be a exact string matching procedure.

We use *micro and macro precision/recall/F1* over the input texts(a text can contain multiple mentions).

*Definition of \\(M\\)* for different problems:

Some issues: entities output by some system might be the redirect page(alias page). A non-redirect page can have multiple redirect pages. We use a *dereference* function \\(d\\) to determine the real entity. 

- For *Concept to Wikipedia(C2W)*: \\(M(e_1, e_2) = d(e_1) = d(e_2)\\).
- For *Disambiguation to Wikipedia(D2W)*: \\(M(e_1, e_2) = d(e_1) = d(e_2)\\).
- For *Annotation to Wikipedia(A2W)*: weak match: \\(M((a_1), a_2) = a_1 \text{ overlaps with } a_2 \land d(a_1.e) = d(a_2.2)\\), strong match: replace overlap with \\(a_1.mention = a_2.mention\\)(exact string matching)

### One side measure


We evaluate mention detection and disambiguation **separetely**.

Mention evaluation: checking overlap only. fuzzy somehow.

### Similarity between systems(across documents/datasets)

Nice to know.

Measure the similarity based on the output.

More details in paper.


## Results

Huom: *AIDA-CoNNL* and *AQUAINT* annotate only a subset of the mentions.

In general, AIDA precision is impressive, however recall is not as good.

Mention detection: best F1, ~70%. AIDA's precision high (~90%), low recall(~40%). TAGME mention detection recall is impressive(78%). It uses distilled anchor corpus.
Entity match: AIDA's precision is impressive.

Speed: AIDA is slow(maybe due to NER process) while TAGME is  fast.

## Comment

- AIDA sucks at mention detection(low recal). Its general precision is impressive
- TAGME is good at mention detection(high recall). It uses distilled anchor corpus. Can we augment AIDA with that?

