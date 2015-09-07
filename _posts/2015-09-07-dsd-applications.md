---
layout: post
title:  "Dense Subgraph Discovery: Applications"
date:   2015-09-07 10:04:15
categories: paper
tags: graph dense-subgraph-discovery
---

## Problem definition

Given a graph (network), *static* or *dynamic*, find a subgraph that has many edges and is *densely* connected.

## Example Application

### community detection

Detect densed connected webpages as they are usually of the same thematic group.

Or help organizational reconstruction by analyzing the employee email network.

Or recommend potential friends from the same community.

- Dense subgraph extraction with application to community detection.
- Kumar, R., Raghavan, P., Rajagopalan, S., and Tomkins, A. (1999). Trawling the Web for emerging cyber-communities. Computer Networks, 31(11{16):1481{1493.}
### Community search 

Given a set of nodes, find the densely connected subgraph that contain the nodes.


- M. Sozio and A. Gionis. The community-search problem and how to plan a successful cocktail party. In KDD, pages 939{948, 2010.}

#### Organizing social events

Given a list of persons you'd like to invite to a party, find other densed connected friends/acquitances of those persons.

Or to organize a workshop, you already have a few seed candidates, but you'd like to invite more.

#### Tag suggestion

Given a set of tags typed by the user for his blog article, recommend more relevant tags.

#### Task driven team formulation

- F. Bonchi, F. Gullo, A. Kaltenbrunner, and Y. Volkovich. Core decomposition of uncertain graphs. In KDD, 2014.


### Story detection

Find set of entiy nodes(person/location/organization, etc) that are densely connected and popular.

- A. Angel, N. Sarkas, N. Koudas, and D. Srivastava. Dense subgraph maintenance under streaming edge weight updates for real-time story identication. PVLDB, 5(6), 2012.

Also another about event detection. What's the difference?

- Rozenshtein, P., Anagnostopoulos, A., Gionis, A., and Tatti, N. (2014a). Event detection in activity networks. In Proceedings of the 20th ACM SIGKDD international conference on Knowledge discovery and data mining.


### Fraud detection / graph based anomaly detection

Detect the same set of users that like the same set of pages at almost the same time.

- Beutel, A., Xu, W., Guruswami, V., Palow, C., and Faloutsos, C. (2013). Copycatch: stopping group attacks by spotting lockstep behavior in social networks. In Proceedings of the 22nd international conference on World Wide Web, pages 119{130.}


### E commerce - sub-markets detection

Context: advertisers by querys for some search engine(eg, Baidu)

Detect the advertisers and queries that are densely connected.

### Hierarchical dense subgraphs detection

Similar to find hierarchy of clusters

- Saryuce, A. E., Seshadhri, C., Pinar, A., and Catalyurek, U. V. (2015). Finding the hierarchy of dense subgraphs using nucleus decompositions. In Proceedings of the 24th International Conference on World Wide Web, 


### Point-to-point distance query

Example: Google Map Directions, routing in sensor network, indorr/terrain navigation

For large graph, two steps are performed, preprocessing and querying.

Hierarchical hub labeling is DSD problem.


- Delling, D., Goldberg, A. V., Pajor, T., and Werneck, R. (2014). Robust distance queries on massive networks. In Algorithms-ESA 2014, pages 321{333. Springer.}


### Frequent item set mining

Similar case: frequent item mining in transaction data(user buys what).

Transaction data -> binary data -> bipartite graph

frequent itemsets -> bi-cliques -> DSD


### social piggybacking

- Gionis, A., Junqueira, F., Leroy, V., Serani, M., and Weber, I. (2013). Piggybacking on social networks. Proceedings of the VLDB Endowment, 6(6):409{420.}


### Graph compression

Compressed graphs(through virtual node) are faster for graph mining tasks, which involve matrix-vector computation.

- Karande, C., Chellapilla, K., and Andersen, R. (2009). Speeding up algorithms on compressed web graphs. Internet Mathematics, 6(3):373{398.}

### Graph visualization

- Alvarez-Hamelin, J. I., Dall'Asta, L., Barrat, A., and Vespignani, A. (2005). Large scale networks ngerprinting and visualization using the k-core decomposition. In NIPS.


### Some distributed graph mining library

- Kang, U., Tsourakakis, C. E., and Faloutsos, C. (2009). Pegasus: A peta-scale graph mining system implementation and observations. In Data Mining, 2009. ICDM'09. Ninth IEEE International Conference on, pages 229{238. IEEE.}

## Next

As in the tutorial:

- Density measures
- Algorithms for static graphs
- Algorithms for dnyamic graphs


Streamming algorithsm perhaps?
