---
layout:     post
title:      CS423 Distributed Information Systems
subtitle:   École Polytechnique Fédérale de Lausanne
date:       2020-08-30
author:     TC YU
header-img: assets/img/Crypto_title.png
catalog: true
tags:
  - machine learning
categories: Courses
---

$$
\def\abs#1{\left|\,#1\,\right|}
$$

# CS423

> [GNU General Public License v3.0](https://github.com/zifeo/EPFL/blob/master/LICENSE) licensed. Source available on [github.com/zifeo/EPFL](https://github.com/zifeo/EPFL).

Spring 2017: Distributed Information Systems

[TOC]

## 0 Overview

### Information system

- information system : software that manages a model of some aspect of real world within a distributed computer system for a given purpose
- real world aspect : physical phenomena, social organization, humman thought
- model : set of constants (identifiers), functions (relations), axioms (constraints)
  - representation of functions : algorithm (implicit), enumaration (explicit) = data
  - interpretation function : homomorphic function (preserve form) from model constant to real-world objects, $I(f(·,\ldots,·))=f_{real}(I(·),\ldots,I(·))$
  - computer system representation : data model that use data structure and operations
- definitions : data model $D$, database $DB$, data definition language $DDL$, data manipulation language $DML$, schema $S$
- abstraction level
  - knowledge : schema that evolve dynamically, knowledge graph
  - structured data : according to a schema, relational data, rdf, xml
  - unstructured data : measurement data, text, video
- data independence : same data interpreted in different ways
  - logical vs physical realization : use interface 
  - pragmatic layer : user/community specific (social utility management), evaluation
  - semantic layer : domain specific (information system), interpretation
  - syntactic layer : domain independent (database system), measurement
  - physical layer : storage (operating system)

### Data management

- data management tasks : e.g. row vs column store
  - efficient : storage, indexing, search, aggregation
  - peristence
  - consistency
- optimizing access database : physical design (frequencies, predicates, before database access), declarative query optimization (logical, index exploitation, during database access) 
- safe storage and update : concurrency, recovery
- transaction : isolation (not inter-user influence), atomaticity and durability (failures do not affect)
- modelling architecture : ANSI
  - conceptual schema : domain specifc, abstract models
  - logical schema : data models
  - physical schema : physical storage on disk, network
- information system modelling
  - semantic layer : domain specific, information system with interpretation
  - syntactic layer : domain independent, database system
  - physical layer : operation system connecting to database

### Information management

- information management tasks
  - retrieval : given a model, find specific data
  - data mining : given data, find a model, higher level abstractions for lower level data
  - conceptual modeling : analyze real world and specify model
  - evaluation : given a model, evalute it against reality
  - control, output : adapt system
  - monitoring, input : collect data 
- utility of information : value depends on importance and quality of decision

### Distributed information systems

- centralization vs distribution : locality, scalability, parallelism
  - heterogeneous information systems : different models, require interoperable IS
  - autonomy : distribution of control
- distributed data management
  - partitioning
  - replication and caching
  - data access : push, pull, indexing, query distribution
  - communication model
    - unicast : point-to-point, request-reply
    - multicast : propagate request to mulptile receivers (gossiping)
    - broadcast : wireless channels
  - dissemination : periodic, conditional, ad-hoc
- data overload : usefulnes, starvation when data supply less than data demand
- semantic heterogeneity : real world aspect modelled differently, relating different model require often humain, caused by automony
  - standardization : mapping though standards
  - ontologies : mediated mapping, explicit specification of conceptualization of real world
  - mapping : direct mapping, detect correspondance, resolve conflict, integrate schema
- syntactic heterogeneity : same data represented using different data models (xml, graphs, rdf)
- evaluating : trust (reputation-based), quality of information (pagerank), privacy (obfuscation)
- distributed control : self organized, coordination in large-scale systems needs to be decentralized
- big data : volume, velocity, variety, veracity

## 1 Information retrieval 

![](./assets/img/cs423-0.jpg)

- information retrieval : task of finding in large collection of documents those that satisfy user needs
- document constituents
  - social : people, authors, consumers, mentionned, social networks, communities, influence as relationship
  - content : text, media, search, clustering, topics, classification as relationship
  - concept : general ideas, explicit annotation, entities extracted, taxonomies or ontologies as relationship
- retrieval model
  - determines : structure of document, query respresentation and similarity matching function
  - relevance : no objective measure, reflect right topic, user need, authority, recency
  - browser : retrieval ranked result interpreted by system, browsing as interpretation of information by human
- evaluation : relevant document R, answer set A, using TREC dataset
  - unranked result set : all elements are equally important
  - recall : $R=\frac{tp}{tp+fn}=P(retrieved\mid relevant)$
  - precision : $P=\frac{tp}{tp+fp}=P(relevant\mid retrieved)$
  - F-measure : weighted harmonic mean $F=\frac{1}{\alpha\frac{1}{P}+(1-\alpha)\frac{1}{R}}\in[0,1]$
  - F1 : balanced F-measure with $\alpha=\frac{1}{2}$ give $F1=\frac{2PR}{P+R}$
  - arithmetic mean : $\frac{tp+tn}{tp+tn+fp+fn}$, not suitable
  - ranked retrieval : interpolated precision $P_{int}( R)=\max_{R'\ge R} R'$
  - mean average precision : $MAP(Q)=\frac{1}{|Q|}\sum_{j=1}^{|Q|}\frac{1}{m_j}\sum_{k=1}^{m_j}P(R_{jk})$ with $Q$ set of queries, with $R_{jk}$ top $k$ documents for query $q_j$
  - specificity : $S=\frac{tn}{fp+tn}=P(notRetrieved\mid notRelevant)$, steeper the better, ROC curve

### Text-based information retrieval

![](./assets/img/cs423-0.1.jpg)

- text-based information rertieval :
  - full-text : ignore grammar, meaning, keep only word, layout and metadata can be taken into account
  - pre-processing : tokens, stopwords, stemming, manual indexing
  - inverted file : indexing
- basic concepts
  - document : $d$, express ideas
  - query : $q$, information need
  - index term $k$ : semantic unit (word, short phrase, root of a word)
  - database : $DB$, collection of $n$ documents $d_j\in DB$
  - vocabulary : $T$, collection of $m$ index terms $k_i\in T$
  - index term importance : $d_j=(w_{1j},\ldots,w_{mj})$ with weights $w_{ij}\in[0,1]$ 
  - similarity coefficient : $sim(q, d_j)$ estimate relevance of $d_j$ for query $q$
  - term-document matrix : contains only terms that occur multiple times, no stop word), term (vertical) vs documents (horizontal)
- boolean retrieval : which term should be present
  - similarity computation
    - determine disjunctive normal form : $q=ct_1\,OR\,\ldots\,OR\, ct_l$ with $ct=t_1\, AND\,\ldots\, AND\, t_k$ and $t_k\in\{t, NOT\, t\}$
    - create weight vector $vec(ct)=(w_1,\ldots,w_m)$ for each conjunctive term
      - $w_i=1$ is $k_i$ occurs in $ct$
      - $w_i=-1$ if not $k_i$ occurs in $ct$
      - $0$ otherwise
    - if one weight vector of conjunctive term matches document : $d_j$ relevant ($sim(d_j,q)=1$), $vec(ct)$ matches $d_k$ if $w_i=1\wedge w_{ij}=1$ or $w_i=-1\wedge w_{ij}=0$

### Vector space retrieval

- vector space retrieval : allow ranking, tolerance with non-binary weights
  - similarity computation : $sim(q,d_j)=\cos\theta=\frac{d_j\cdot q}{\abs{d_j}\abs{q}}$
    - $d_j=(w_{1j},\ldots,w_{mj})$ with $w_{ij}>0$ if $k_i\in d_j$
    - $q=(w_{1q},\ldots,w_{mq})$ with $w_{iq}\ge 0$ 
    - $\abs{v}=\sqrt{\sum_{i=1}^m v_i^2}$
  - term frequency : $tf(i,j)=\frac{freq(i,j)}{\max_{k\in T} freq(k,j)}$ of term $k_i$ in $d_j$
  - inverse document frequency : $idf(i)=\log\frac{n}{n_i}$ with $n_i$ number of document in which $k_i$ occurs
  - query weight : $w_{ij}=tf(i,j)idf(i)$
    ![](./assets/img/cs423-1.jpg)

### Probabilistic information retrieval

- probabilistic information retrieval : attempt to directly model relevance as probability
  - query likelihood model : $P(d\mid q)$ assume $P(d)$ uniform across collection, $P(q)$ same for all documents
  - language modeling : assume each document generated by language model, probabilities for each ngrams
  - query : $P(q\mid M_d)$ 
  - MLE : $P_{mle}(t\mid M_d)=\frac{tf_{t,d}}{L_d}$ with $tf_{t,d}$ number of occurences of $t$ in $d$ (term frequency) and $L_d$ number of terms in document (document length)
  - model : $P(q\mid M_d)=\Pi_{t\in Q}P_{mle}(t\mid M_d)$
  - smoothed model : $P(t\mid d)=\lambda P_{mle}(t\mid M_d)+(1-\lambda)P_{mle}(t\mid M_c)$ with $M_c$ language model over whole collection, add small weight for non-occuring term $P(t\mid M_d)\le cf_t/T$ for $cf_t$ occurence of $t$ in collection and $T$ total number of terms in collection
    - model : $P(d\mid q)\propto P(d)\Pi_{t\in q}((1-\lambda)P(t\mid M_c)+\lambda P(t\mid M_d))$, tuning can be query-dependent

### Query expansion

- query expansion : increase recall by adding terms
  - local approach : user relevance feedback, use information from current query results
    - user identifies documet as (non)-relevant 
    - collection centroid : $\mu(D)=\frac{1}{\abs{D}}\sum_{d\in D}d$ of document set
    - Rocchio algorithm : find query that optimally separates relevant from non-relevant, $q_{opt}=\arg\max_{q}[sim(q,\mu(D_r))-sim(q,\mu(D_n))]$
    - identifying relevant document : $q_{opt}=\mu(D_r)+[\mu(D_r)-\mu(D_n)]$, but user feedback incomplete, user feedback can be homogeneous
    - smart : practical relevance feedback, $q_{approx}=\alpha q+\frac{\beta}{\abs{D_r}}\sum_{d_j\in D_r}d_j-\frac{\gamma}{\abs{R\setminus D_r}}\sum_{d\not\in D_r}d_j$ with tuning parameters, result $R$ and relevant document $D_r$
    - pseudo-relevance : if no user feedback, choose top-k and apply SMART, works good unless horribly failed (query drift)
  - global approach : query expansion, use information from a document collection, use query independent resource
    - automatic thesaurus generation : similary between two words, co-occur, gramatical relation	
    - query logs : exploit correlation, users extends query, users refer to same result

### Indexing for information retrieval

- indexing for information retrieval
  - inverted files : word-oriented mechanism, semi-static collection, optimized for search
    - inverted list : $l_k=<f_k:d_{i_1},\ldots,d_{i_{fk}}>$ with $f_k$ number of document in which $k$ occurs, $d$s document identifiers of document containing $k$
    - lexicographically ordered sequence : $IF=<i,k_i,l_{k_i}>$
      - heap's law : $0.4<\beta<0.6$ 
        ![](./assets/img/cs423-2.jpg)
        ![](./assets/img/cs423-3.jpg)
        ![](./assets/img/cs423-4.jpg)
  - searching
    - vocabulary search
    - retrieval of occurences
    - manipulation of occurences 
  - construction
    - search phase : trie storing for each word a list of its occurences, sequentially
    - storage phase : write list of occurences contiguously to disk, write sorted vocabulary, O(n)
    - index : can be merged if not fit in memory $O(n\log_2(n/M))$ with memory $M$, map-reduce
  - granularity
    - coarser : text spanning multiple document
    - finer : paragraph, sentence, words
    - general rule : finer granularity the less post-processing but larger index
  - index compression : replace ordered document identifier in inverted list by their differences (10-15%)

### Distributed retrieval

- distributed retrieval : aggregate weights for all documents
  - Fagin's algorithm : enrties in posting lists sorted according to tf-idf weights, scan in parallel all list in round-robin till $k$ documents detected in all list, lookup missing weights for documents not seen in all lists, select top-$k$ elements, $O((k n)^{1/2})$ (assuming non correlated)

### Linked-based ranking

- linked based retrieval
  - hypertext : anchor text as context, hyperlink as quality signal
  - anchor text : surrounding a hyperlink
  - scoring : weight depdending on the authority of the anchor page
  - nepotistic : promoting your own family members, can give lower weights to links within same site
- pagerank
  - citation analysis
    - frequency : importance
    - co-citation : related
    - indexing : who is this author cited by
    - authority of sources : impact factor of journals
  - relevance
    - number of referrals (incoming links)
    - number of high relevance refferals
  - scoring
    - link-based : random equiprobable walk, long-term visit rate is score
    - teleporting : random web page at dead end, probability to jump at each visit
    - model : $P(p_i)=\sum_{p_j\mid p_j\to p_i}\frac{P(p_j)}{C(p_j)}$ for $N$ pages with outgoing links $C(p)$ and probability to visit $P(p_i)$
    - matrix equation : $R_{ij}=\frac{1}{C(p_j)}$ if $p_j\to p_i$ else $0$, $p=(P(p_1),\ldots,P(p_n))$, $p=Rp$ for $||p||_1=\sum p_i=1$, relevance is highest eigenvalue
  - pagerank : $P(p_i)=c(\frac{1-q}{N}+q\sum_{p_j\mid p_j\to p_i}\frac{P(p_j)}{C(p_j)})$ for $c\le 1$, $p=c((qR+(1-q)E))p=c(qRp+\frac{(1-q)}{N} e)$ with $E=1/N$ square matrix and $E\cdot p=e$
    ![](./assets/img/cs423-5.jpg)
- HITS : hyperlink-induced topic search
  - idea : for a query, find two sets of inter-related pages instead of lists of page
    - hub page : good lists of links for subject
    - authorative : pages occuring recurrently on good hubs
  - best : broad topic, post processing
  - computing : 5 iterations to converge in practice
    - select $N$ pages containing relevant hubs and authorities
    - for each page $p$ compute hub score $H(p_i)=\sum_{p_i\to p_j} A(p_j)$ and authority score $A(p_i)=\sum_{p_j\to p_i} H(p_j)$, normalize sum of square = $1$
    - initialize $h(p)=1/N^2$ and $a(p)=1/N^2$, iteratively update and return high scores
  - eigenvalue : $y=Lx$ and $x=L^\top y$ give $y^*$ eigenvector of $LL^\top$ and $x^*$ eigenvector of $L^\top L$, always converge (power iteration) to highest one
  - root set : all pages mentioning the query
  - base set : page that points to root set or is pointed by root set
  - issues : structural anomalies of link structures, tropic drift, mutually reinforcing affiliates
- social network analysis : pagerank and hits
- link indexing
  - connectivity server : stores mapping in memory from URL to outlinks, URL to inlinks
  - adjaceny lists : set of neighbors of a node, each number represented by an URL, 320 GB for actual web (10 pages per links), but locality (gap encoding, gamma encoding) and similarity is good (copy data from similar lists, reference list, copy lists)

## 2 Data mining

- big data challenge
  - data : accessible, legal, format
  - questions : searching for insights
  - algorithms : smart and efficient
  - systems : handling such load
- local properties
  - patterns : pattern mining, association rules
- global models
  - descriptive structure of data : clustering, information retrieval
  - predictive function of data : classification, regression
  - exploratory data analysis : interactive tools, visualisation
- data mining algorithm
  - pattern structure/model representation
  - scoring function
  - optimisation and search : tune parameters
  - data management : very large dataset

  ### Association rule mining

- association rule
  - form $body\to head\,[support, confidence]$ with $body$ and $head$ being $predicate(x,x\in\{items\})$
  - multimensional : $body_1\wedge body_2$, can be decomposed to multi single rules
  - support : probability that body and head occur in transaction $p(\{body, head\})$
  - confidence : probability that if body occurs, also head occurs $p(\{head\}\mid\{body \})$
  - definition
    - itemset : subset of all items $I$
    - transaction : $(tid, T)$ with $T\subset I$
    - database : $D$ set of all transcactions
    - association rule : $A\to B\,[s,c]$
      - $A,B\subseteq I$
      - $A\cap B$ empty
      - $s=p(A\cup B)=count(A\cup B)/|D|$
      - $c=p(B\mid A)=s(A\cup B)/s(A)$
  - problem : find all rules s.t. $s>s_{min}$ (high support) and $c>c_{min}$ (high confidence)
  - two step approach
    - find frequent itemsets : $J\subseteq I$ s.t. $p(J)>s_\min$, if $A\cup B$, then only $A\to B$ can be an associate rule, any subset of frequent itemset is also frequent (apriori property), find frequent itemsets with increasing cardinality from $1$ to $k$ to reduce search space
    - select pertinent rules : $A\to B$ s.t. $A\cup B=J$ and $P(B\mid A)> c_\min$
    - apriori property : any subset of frequent itemset is also frequent itemset
      - union of two $k-1$ that differs only by one item is candidate
      - join algorithm : sort itemset in $L_{k-1}$, find all pairs with same first $k-2$ items, but different $k-1$th item, join two itemset, 
      - prune : not frequent ones (or not existing)
        ![](./assets/img/cs423-5.1.jpg)
        ![](./assets/img/cs423-5.2.jpg)
        ![](./assets/img/cs423-6.jpg)
        ![](./assets/img/cs423-7.jpg)
  - alternative measures of interest : interesting rule those with high positive or negative interest values
    - added value : $AV(A\to B)=confidence(A\to B)-support(B)$
    - lift : $lift(A\to B)=\frac{confidence(A\to B)}{support(B)}$
  - quantitative attributes : transforming quantitative (numeric ordered values) into categorical ones, rules depends on discretisation
    - static discretisation : predefined bins
    - dynamic discretisation : based on data distributions
  - apriori for large dataset
    - transaction reduction : omit non frequent one in subsequent scans
    - sampling : randomly sample with probability $p$, detect frequent itemset with support $ps$, eliminate false positive by counting frequent items on complete data (if sorted, take first ones), false negative can be reduced by choosing lower threshold $0.9ps$
    - partitioning : SON algorithm 
      - divide in $1/p$ partitions, repeatedly read partition into main memory
      - detect in-memory algorithm to find all frequent itemsets with support threshold $ps$
      - itemset becomes candidate if found to be frequent in at least one partition
      - second pass count candidate items and determine which are frequent in entire set
      - monotonicity idea : itemset cannot be frequent in full set without being frequent in at least one partition
  - FP growth : frequent itemset discovery without candidate generation
    - build FP-tree datastructure
      - compute support for each item : sort items in order of decreasing support (compact tree)
      - construct FP : expand one itemset at a time
      - introduce links between similar labels
    - extract frequent itemsets directly from FP-tree 
      - bottom-up approach : for each item extract tree with paths ending in the item, start with lowest support
      - conditional : FP-tree that contains selected items, remove nodes of the item, remove nodes with unsufficient support count
        ![](./assets/img/cs423-7.1.jpg)
        ![](./assets/img/cs423-7.2.jpg)
    - advantages : only 2 passes, compresses dataset, much faster than apriori
    - disadvantages : less efficient with high support threshold, in-memory, difficult to distribute

### Information retrieval advanced models for test representation

- vector spacew retrieval : bad handling of synonym (car and automobile, poor recall) and homony (apple, poor precision)
- dimensionality reduction : map documents and queries into lower-dimensional space composed of higher-level concepts
- concept space : normalized concept vector
- term-document matrix : $M_{ij}$ with $m$ rows (terms) and $n$ columns normalized (documents) given a weight $w_{ij}$ associated with $t_i$ and $d_j$ (can be based on tf-idf weighting scheme)
  - computing ranking : $M^\top\cdot q$
- singular value decomposition : $M=KSD^\top$ with $KK^\top=I=DD^\top$ having orthonormal columns and $S=r\times r$ diagonal matrix of singular values in descreasing order ($r=\min(m,n)$)
  - $K=eigen(MM^\top)$
  - $D=eigen(M^\top M)$ 
  - complexity : $O(n^3)$ if $m\leq n$
  - $s_i$ : length of semi-axes of hyperellipsoid $E=\{Mx \mid ||x||_2=1\}$
- latent semantic indexing : selection only the largest $s$ from SVD giving $M_s=K_sS_SD_s^\top$, poor statisical explanation
  ![](./assets/img/cs423-7.3.jpg)
- cosine similarity : compare columns $(D_s^\top)_i$ and $(D_s^\top)_j$, query $q$ applies same transformation (treated as additional document vector)
  - mapping $M$ to $D$ : $M=KSD^\top$ as $D=M^\top KS^{-1}$ giving $q^*=q^tK_sS_s^{-1}$
  - similarity : $sim(q^*,d_i)=\frac{q^*\cdot (D_s^\top)_i}{|q^*||(D_s^\top)_i|}$ 
- probabilistic latent semantic analysis : based on Bayesian Networks
- latent dirichlet allocation : based on Dirichlet distribution, state-of-the-art, interpretable
  - idea : assume document collection randomly generated from known set of topics (generative model, for each doc, choose mixture of topics, sample a topic, sample a word), given document collection, reconstruct topic model
- word embeddings : neighborhood of word $w$ express a lot about its meaning, model how likely a word and a context occur together
  - similarity based representation : most successful idea, two words similar when they have similar contexts (syntactic, semantic) 
  - context : $C(w)$ giving word context occurence $(w,c),c\in C(w)$
  - model : two matrices $W^{(1)}$ and $W^{(2)}$ to map words and context words
    ![](./assets/img/cs423-8.jpg)
  - probability : $p(D=1\mid w,c;\theta)=\frac{1}{1+e^{- v_c\cdot v_w}}=\sigma(v_c\cdot v_w)$, whether conext comes from data
  - goal : find $\theta$ s.t. overall probability is maximized $\theta=\arg\max_{\theta}\Pi_{(w,c)\in D}P(D=1\mid w,c,\theta)\Pi_{(w,c)\in\tilde D}P(D=0\mid w,c,\theta)$ (require negative sample $\tilde D$)
  - SGD : $\theta'=\theta + \alpha\Delta_\theta J_t(\theta)$ with $J(\theta)=-\log(\sigma(v_c\dot v_w))-\sum_{k=1}^K\log(\sigma(v_{c_k}\cdot v_w))$ only over row containing word
  - negative sample : obtained from $P_n(w)=V\setminus C(w)$, empirical approach approximate probability by sampling few non-context words or choose probability $p_w^{3/4}$ instead of $p_w$
  - low-dimensional representation : $W=w^{(1)}+W^{(2)}$
- CBOW : continuious bag of words models, predict words from context
- GLOVE : exploits ratio of probabilities to capture semantic relationships among terms

### Clustering

- clustering : assign objects described by attributes to a class
- model : partition a set of objects into clusters, unsupervised
- similarity
  - intra-cluster : high
  - inter-cluster : low
- heuristic algorithms : $n$ objects, $k$ clusters, $t$ iteration
  - K-means : clustered by point whose mean distance minimal, $O(tkn)$, local optimum
  - K-medoids : clustered by object whose mean distance minimal
  - K-medians : clustered by point whose median distance minimal
- partioning : given database $D$ of $n$ object, split $D$ into $k$ sets $C_1,\ldots,C_k$ s.t. $C_i\cap C_j=\emptyset$
- score function : minimize $J=\frac{1}{n}\sum_{i=1}^k\sum_{x_j\in C_i}||x_j-\mu_i||^2$, $\mu_i=\frac{1}{|C_i|}\sum_{x_j\in C_i} x_j$
- categorical attributes : use matching coefficient, distance = #pair-wise mismatches / #features
- distance function : $d(x,y)\ge 0$, $d(x,y)=0\iff x=y$, $d(x,y)=d(y,x)$, $d(x,z)\le d(x,y)+d(y,z)$
- density-based clustering : handle noise, cluster in one scan, discover non convex, no need to define cluster number
  - distance metric : $\epsilon$-neighborhood $N_\epsilon(q)=\{p\mid d(p,q)<\epsilon \}$
  - core point : if $|N_\epsilon(q)|\ge\mu$
  - directly density-reachable : $p$ from $q$ if $p\in N_\epsilon(q)$ and $|N_\epsilon(q)|\ge\mu$ (not core point but reachable is called border point, otherwise outlier)
    ![](./assets/img/cs423-8.1.jpg)
  - directed graph : induced
  - density-reachable : $p$ from $q$ if there is chain $p_1,\ldots,p_n$ $p_1=q$ $p_n=p$ s.t. $p_{i+1}$ directly density-reachable from $p_i$
    ![](./assets/img/cs423-8.2.jpg)
  - density connected : $p$ to $q$ if there is point $r$ s.t. both $p$ and $q$ density-reachable from $r$, symmetric
    ![](./assets/img/cs423-8.3.jpg)
  - cluster : set of clusters unique, not necessary disjoint, $C$ satisfies
    - maximality : if $q$ in $C$ is core point, $p$ also in $C$ if density reachable
    - connectivity : any two points in $C$ must be density connected
  - DBSCAN
    - initialization : $V_{core}$ set of core points, $P$ all points, set of clusters $C=\{\}$, $O(n^2)$
    - construction : while $V_{core}$ not empty, $O(n^2)$
      - select point $p$ from $V_{core}$ and construct $S(p)$ set of all points density reachable from $p$ (breadth first search on $G$ starting from $p$)
      - $C=C\cup\{S(p)\}$
      - $P=P\setminus S(p)$
      - $V_{core}=V_{core}\setminus S_{core}(p)$ where $S_{core}(p)$ core points in $S(p)$
      - remaining points : unclustered
- online incremental clustering

### Mining social graphs

- graph : explicit relationships
- graph cluster : communities, interests, level of trust
- clique : complete subgraph
- linked
  - heavy intra-linked : high intra-cluster similarity
  - scarcely inter-linked : low inter-cluster similarity
- hierarchical clustering : iteratively identifies groups of nodes with high similarity
  - agglomerative algorithm : merge node and communities with high similarity
  - divisive algorithms : split communities by removing links that connect nodes with low similarity
- Louvain algorithm : agglomerative
  - modularity : measure for community quality, higher the better $\sum_{c\;in\; communities}(\#edge\;within\;c - expected\;\#edges\;within\;c)$
    - expected number of edge : unweighted, uniform random graph (null model), total number of edge $m$, $k_i$ degree (outgoing edges of node $i$, $2m$ edge ends), node $j$ has an end with $k_j/2m$ 
    - measure : $Q=\frac{1}{2m}\sum_{i,j}(A_{i,j}-\frac{k_ik_j}{2m})\delta(c_i,c_j)$ with $A_{ij}$ number of edges between $i$ and $j$, $c_i$ communities of node $i$, $Q\in[-1,1]$, significant $0.3-0.7$
  - first : find small communities by optimizing modularity locally (maximally) on all nodes, try all possibilities and choose the best
  - then : each small community into a new community, repeat
    ![](./assets/img/cs423-8.4.jpg)
  - communities : sets of nodes with many mutul connection, much less connections to the outside
  - optimum : modularity determine best level to cutoff hierarchical
  - complexity : $O(n\log n)$
- Girvan-newman algorithm : divisive, decomposition by splitting edges with highest separation capacity
  ![](./assets/img/cs423-8.5.jpg)
  - betweenness measure : how well they separate communities, number of shortest paths passing over the edge
    - BFS : top down, for each node BFS, $\#shortest paths(A-X)=\sum_{P\; parent\;of\; X}\# shortest paths(A-P)$
    - edge flow : edge flow, $weighttosplit(X)=1+\sum_{c\; child\; of\; X}\# edgeweight(X-C)$
    - BFS for each node, determine edge flow for each edge, sum up flow values (divide by 2 edge values as computed in both direction)
      ![](./assets/img/cs423-8.6.jpg)
      ![](./assets/img/cs423-8.7.jpg)
  - random walk betweenness : pair $n$ and $m$ chosen at random, walker stats at $m$ follow each adjacent link uniformly until $n$, betweenness is probability of crossing $i\to j$ after all possible choices for starting nodes $m$ and $n$
  - until no edges left : remove edges with highest betweenness
  - complexity : one link $O(n^2)$, all links $O(L n^2)$, sparse matrix $O(n^3)$

### Classification

- descriptive modeling : clustering
- predictive modeling : bayes, k-nearest, random forest, svm, boosted trees, etc.
- classification : supervised
  - input : set of objects (database $D$) with categorical/numerical attributes and one class label $C$
  - model : $X^d\to C$
  - output : returns class given the attribut
  - learnt : training set
  - evaluated : test set
  - prediction : unknown labels
- characteristics
  - predictive accuracy
  - speed and scalability : time to build, use the model, in memory, disk
  - robustness : handling noise, outliers, missing value
  - interpretability : understand the model, compactness of the model
- decision tree induction : flow chart lie tree
  - score function : accuracy 
  - optimisation : top-down tree construction + pruning
  - construction : examples paritioned recursively based on selected most discriminative attributes
  - stop partitioning : if all samples belong to same class (leaf), no attributes left (majority voting to assign class), no samples left
  - attribute selection entropy : $H(P,N)=-\frac{P}{P+N}\log_2\frac{P}{P+N}-\frac{N}{P+N}\log_2\frac{N}{P+N}$
  - attribute entropy : for attribute $A$ partitioning into $S_1,\ldots, S_v$, $H(A)=\sum_{i=1}^v\frac{P_i+N_i}{P+N}H(P_i,N_i)$, select lowest
  - information gain : $Gain(A)=H(P,N)-H(A)$, select highest
  - pruning : reduce overfitting by stop partitioning when large majority is positive
    - build full tree and replace nodes with leaves labelled with majority class if classification accuracy does not change
    - minimum description length principle : best model $M_i$ minimize $L(M)+L(D\mid M)$ where $L(M)$ is lenght in bits of the description of the model (#nodes,#leaves,#arcs) and $L(D\mid M)$ is length in bits of description of encoded data with the model (#misclassifications)
  - extracting classification : if-then rules
  - continous attributes : use binary decision trees
    - sort data according to attribute value
    - determine value that maximizes the information gain by scanning though the data items
    - scalability : persorting data and maintaining order, for everyt attribute separate and presorted table kept (use hashtable to maintain partition attribution)
      - selected attribute : partition table into two subtables
      - other attributes : use temporary hash table to associate eac dataitem to its partition, then scanned and partitionned 
  - characteristics : automatic feature selection, minimal data preparation, non-linear model, easy to interpret, senstive to perturbation, overfit
- ensemble methods : collection of simple/weak learner, combine results to make strong learner
  - bagging : train learner parallel on different samples, combine by voting or averaging
  - stacking : combine models using a second stage learner like regression
  - boosting : train learner on filtered output of other learners
- random forest : learn $K$ different decision trees from independent samples, vote between different learners so model not too similar
  - aggregate output : majority vote
  - sampling strategies : subset of different data, subset of attributes
  - algorithm : typical parameters $m\approx\sqrt(p)$, $K\approx 500$ for $p$ total attributes
    - draw $K$ bootstrap samples of size $N$ from original dataset with replacement (bootstrapping)
    - contruct decision tree and select a random set of $m$ attributes out of $p$ to infer split (feature bagging)
  - characteristics : complex decision without overfitting, popular for dense data (thousand of features), easy to implement, good for map reduce, worse than deep neural net, need many pass, hard to balance

### Classification methodology

- credibility : trustworthiness (well-intentioned, truthful, unbiased) + expertise (knownledge, experienced, competent)
- data collection and preparation
  - feature identitifcation : domain knownledge is needed
    - numerical : maybe discretisation
    - ordinal
    - categorical 
  - labelling : time consuming, expensive, ask expert, ask crowd, complementary source of information (distant learning)
    - crowd-workers
      - truthful : expert, normal
      - untruthful : sloppy (limited knownledge, misunderstanging), uniform spammer, random spammer 
    - non-iterative aggregation algorithms : process answers matrix and produce estimate of most likely answer to be correct
    - majority decision : MD, no preprocessing, $P(x_j=l)=\frac{1}{N}\sum_i^N(1\mid a_i(x_j)=l)$ with $x_j$ object to label $l$,
    - honey pot : HP, insert known labels, remove workers that fail at correctly labelling more that $m$%, then majority decision
    - iterative aggregation algorithms : estimate worker expertise from answers
    - expectation maximisation : EM
      - e-step : estimate label from answers of workers, $P(x_j=l)=\frac{1}{\sum_{i=1}^N w_i}\sum_{i=1}^N (w_i\mid a_i(x_j)=l)$
      - m-step : estimate reliability of workers from consistency of answers, expertise $w_i=\frac{1}{M}\sum_{j=1}^M(1\mid a_i(x_j)=\arg\max P_l(x_j=l))$ 
  - discretization
    - unsupervised discretisation : equal with, equal frequency, clustering
    - supervised discretisation : independence test $x^2$ statistics, $x^2=\sum_i\sum_j \frac{(O_{ij} - E_{ij})^2}{E_{ij}}$ with $O_{ij}$ observed frequency and $E_{ij}$ expected frequency, independent if $P(x^2\mid DF=1)>0.05$ with $DF$ degree of freedom ($=(\#row - 1)(\#cols -1)$), merge interval
  - feature selection : ${N\choose M}$ subsets, select $M$ optimal features, correlation is not causality, collective relevant features may look individually irrelevant
    - filtering : consider feature as independent, rank them according to predictive power ($P(x^2\mid DF=n-1)$ give rank measure)
      - mutual information : numerical $I(F;C)=H( C)-H(C\mid F)=H(F)+H( C)-H(F,C)$
    - wrapper : consider dependencies among features, create classifier at each iteration and evaluate its performance, stop if no improvement 
  - feature normalization : classifier are sensitive to scale
    - standardization : $x_i'=\frac{x_i-\mu_i}{\sigma_i}$ normal distribution
    - scaling : $x_i'=\frac{x_i-m_i}{M_i-m_i}$ map to interval $[0,1]$, bad with outliers
- model training, selection and assessment 
  - selecting performance metrics
    ![](./assets/img/cs423-8.8.jpg)
    - accuracy : $A=\frac{TP+TN}{TP+TN+FP+FN}=\frac{TP+TN}{N}$, when classes not skewed, error same importance
    - precision : $P=\frac{TP}{TP+FP}$
    - recall : $R=\frac{TP}{TP+FN}$
    - F-score (F1) : $F1=2\frac{PR}{P+R}$
    - F-beta score : $F_\beta=\frac{(1+\beta^2)PR}{\beta^2P+R}$
  - model selection : tune parameters (regularisation factor, threshold, distance function, number of neighbours)
    - train, test (model assessment), validation (model selection)
    - loss function
      - categorical output : $J=\sum_{i=1}^n \#(y\not = f(x_i))$
      - real value output : $J=\frac{1}{n}\sum_{i=1}^n(y_i-f(x_i))^2$, absolute
  - organizing training and test data
    - k-fold cross validation : $K-1$ training set, $1$ validation, avergage of $K$
    - leave one out cross validation : at extreme k-fold, $N-1$ training set, $1$ validation, avergage of $N$
    - fight skew
      - stratification : validation set as random sample but ensure approximately proportionally represented
      - over and under sampling : including over proportionally number from smaller class (over-sampling) and under proportional number from larger class (under-sampling)
    - good model : good estimate $f:X^d\to Y$ with error $err(f_d,T)=\frac{1}{|T|}\sum_{X\in T}(f_D(X)-y)^2$ with $T$ validation set
    - expected error : $EErr_{train}=E_D[Err(f_D,D)]$
    - test error : $EErr_{test}=E_{D,T}[Err(f_D,T(D))]=bias^2+variance$
      - bias : $bias=E_{D,T}[f_D(X)-y]$, high is a sign of under-fitting (simple model)
      - variance : $variance =E_{D,T}[(f_D(X)-E_D[f_D(X)]^2]$, high is a sign of over-fitting (complex model)

### Document classification

- document classification : unstructured documents, spam filtering, sentiment analysis, document filtering, features
  - words of documents : bag of words, document vector
  - detailed information on words : phrases, word fragments, grammatical features
  - metadata about document and its author
  - challenge : very high dimension, need feature selection (mutual information), dimensionality reduction (word embedding), scalable algorithms
- k-Nearest-Neighbors : vector space model
  - retrieve k nearest neighbors
  - choose majority class label : estimate $P(C\mid D)\approx \#C/k$ probablity document has indeed class $C$
  - small $k$ : (simple model), low bias, high variance
- naive Bayes classifier : probablistic retrieval, bag of words
  - how characteristic $w$ for $C$ : $P(w\mid C)=\frac{|w\in D,D\in C|+1}{\sum_{w'}|w'\in D,D\in C| + 1}$
  - how frequent $C$ : $P( C)=\frac{|D\in C|}{|D|}$
  - Bayes law : $P(C\mid D)\propto P( C)\Pi_{w\in D}P(w|C)$
  - most probable class : $C_{NB}=\arg\max_C(\log P(C )+\sum_{w\in D}\log P(w\mid C))$
- word embeddings :
  - probability $w$ occurs with context word $c$ : $P(D=1\mid w,c;\theta)$
  - consider instead of (word, context) : (class, paragraph)
  - learn : $P(C\mid p)=\frac{e^{v_p\cdot v_c}}{\sum_{C'}e^{-v_p\cdot v_{C'}}}$ 
  - Fasttext: classifier based on word embedding, n-grams (phrases), subword information (character n-grams)

### Recommender systems

- model : users, items ranks items in order of descreased relevance
- collaborative-based : tell me what other people like
  - wisdom of the crowd : users give ratings to items, user with similar tastes in the past will have similar tastes in the future
  - users based : estimate rating $r_U(I)$ : find set of users $N_U$ who liked the same items as $U$ and rated $I$, aggregate ratings of $I$ provided by $N_U$, cold start, not scalable, mean rating $r_x$ of user $x$
    - Pearson correlation coefficient : $sim(x,y)=\frac{\sum_{i=1}^N(r_x(i)-\bar r_x)(r_y(i)-\bar r_y)}{\sqrt{\sum_{i=1}^N(r_x(i)-\bar r_x)^2}\sqrt{\sum_{i=1}^N(r_y(i)-\bar r_y)^2}}$ between $-1$ and $1$
    - cosine similarity : $sim(x,y)=\cos(\vartheta)=\frac{\sum_{i=1}^N r_x(i)r_y(i)}{\sqrt{\sum_{i=1}^N r_x(i)^2}\sqrt{\sum_{i=1}^N r_y(i)^2}}$
    - common aggregation : $r_x(a)=\bar r_x+\frac{\sum_{y\in N_U(x)} sim(x,y)(r_y(a)-\bar r_y)}{\sum_{y\in N_U(x)}|sim(x,y)|}$
  - item-based : replace users by items $N_I(a)$, more stable, can be computed in advance, runtime neighboorhood small $N_I(a)$, item $b$ rated by $x$
    - common aggregation : $r_x(a)=\frac{\sum_{b\in N_I(a)}sum(a,b)r_x(b)}{\sum_{b\in N_I(a)}|sim(a,b)|}$ 
- content-based : show me more of what I liked 
  - find items similar to past items, aggregate ratings of most similar, cold start, recommend more of the same
  - TF-IDF description weight : $w(t,a)=tf(t,a)idf(t)=\frac{freq(t,a)}{\max_{s\in T} freq(s,a)}\log\frac{N}{n(t)}$ with $n(t)$ number of items where term $t$ appears, after pre-processing (stopmwords, stemming, top M terms)
    - cosine similarity : $sim(a,b)=\cos(\vartheta)=\frac{\sum_{t=1}^T w(t,a)w(t,b)}{\sqrt{\sum_{t=1}^T w(t,a)^2}\sqrt{\sum_{t=1}^T w(t,b)^2}}$ with items $a$, $b$, term $t$
    - aggregation : $r_x(a)=\frac{\sum_{b\in N_I(a)} sim(a,b)r_x(b)}{\sum_{b\in N_I(a)}|sim(a,b)|}$
- matrix factorization : $\min_{q,p}\sum_{(u,i)\in M}(r_u(i)-q_i^\top p_u)^2+\lambda(||q_i||^2+||p_u||^2)$, incomplete matrix, SGD

## 3 Knowledge modeling

- implicit knowledge : information retrieval, data mining
- explicit knowledge : knowledge modelling

### Semi-structured data

- schemas : datastructure for databases, relationa, xml, agreement on data structures, consistency, integrity, optimize query
- HTML : too limited, no schema, no constraints, hard to analyze 
- semi-structured data : contains tags, markup to specify semantics, and relate values (e.g. hierarchically), embeds schema into data, email, json, xml
  - application-specific markup : making meaning of data explicit (through tags), XML extensible markup language
  - serialization : canonical encoding into a text
  - schema-less data : flexible, self-contained, but consistency and optimization not feasible
    ![](./assets/img/cs423-8.9.jpg)
- document mode : serialized representation

### Semantic web

- semantic web : extension of current web in which information is given well-defined meaning
  - overcome semantic heterogeneity
    - standardization (integrated approach) : common user-defined, power play enforces it, integrated approach (exists common format, detailled, aggred upon by all parties)
    - translation (federated approach) : mappings, require human, difficult,  federated approach (no common format, accommodate on the fly, no party imposes models, languages and method of work)
    - annotation (unified approach) : create relationships to agreed upon conceptualizations, meta-model, IS-A, unified approach (common format but only meta-level)
  - ontologies : explicit specification of a conceptualization of the real world, proxy representation (annotation)
    - ontology engineering : manual effort, edit and check
    - automatic induction : from large document collections 
    - modeling/encoding : what does an arrow/instance-of/ISA mean? use tag attribute to reference the concept
      ![](./assets/img/cs423-9.jpg)

### RDF resource description framework

- resource decription framework : RDF, graph oriented data model to annotate any kind of XML document, similar to ER model
  ![](./assets/img/cs423-9.0.jpg)
  - statements : about resources (URI-addressable) and literals (XML data)
  - resource form : subject (URI) property object (URI or string)
  - RDF statement : also resources
  - properties : define relationship to other resources or atomic values
  - schema : grammar and vocabulary for semantic domains
  - containers : bag (unordered multi-set), seq (ordered), alt (alternatives, single resource chosen out of given set)
    ![](./assets/img/cs423-9.01.jpg)
  - typing resources : special property rdf:type
  - new resources : special property rdf:ID
  - quantifiers : about, aboutEach
  - reification : introduce resources which serves as representative for a statement
    ![](./assets/img/cs423-9.02.jpg)
- RDF schema
  - categorization : into classes
  - constraints : possible uses of properties (connect resources)
    - domaines : classes of which instances may have a property
    - range : classes of which the instances may be the value of a property
      ![](./assets/img/cs423-9.1.jpg)
      ![](./assets/img/cs423-9.2.jpg)
  - inheritance
    - A subClassOf B : transitive, not reflexive, anti-symmetric (no cycle), many subclass and superclass, subclass inherit all properties of superclass
    - P1 subPropertyOf P2 : if A has property P1 with value B then it has value B with property P2 
- classification
  ![](./assets/img/cs423-10.jpg)

### Semantic web resources

- sematic web resources : wikidata, google knowledge graph,  lined open data example, multi-encoding (json, rdf)
  - wordnet : synonymy/antonymy, herpnymy (hierarchical relationship between words)/hyponymy, meronymy (part-whole relationship, e.g. paper and book)
  - schema.org : big companies behind

### Onotology languages

- ontology languages : OWL, well designed, well defined, compatible with RDF, rich compared to RDF
  - description logics (DL) : fragment of first order predicate logic (FOL), reasoning can be done, describe knownledge in term of concepts and role restrictions used to automatically derive classification taxonomies
    ![](./assets/img/cs423-11.jpg)
  - frame-based systems : central modeling primitive classes with attributes
  - web standards : XML, RDF 
  - class constructor : intersectionOf, unionOf, complementOf, oneOf, allValuesFrom, someValuesFrom, maxCardinality, minCardinality
    ![](./assets/img/cs423-12.jpg)
  - OWL axioms
    ![](./assets/img/cs423-13.jpg)
  - OWL lite : subset of OWL, easy to omplement
  - OWL DL : restriction to FOL fragment, mixing of RDFS and OWL restricted, disjointness of classes, properties, individuals and data values
  - OWL Full : union of OWL syntax and RDF, no restriction

### Information extraction

- populating knowledge bases automatically : extract knowledge from documents, challenge natural language
  - concept : ideas or concrete entities, relationship
  - identifying : explicit (twitter tag, keywords) or extract
- key phrase extraction : automatic selection of important and topical phrases fro document body
  - candidate phrases : removing stop word, use word n-grams, part-of-speech
  - baseline ranking approach : ranking according to tf-idf
  - advanced approach : use many structural, syntactic document features, external resources 
- named entity recognition : find and classify names of people, organizations, places, brands mentionned in documents
  - uses : sentiment attributed to produtcts, anchors
  - sequence labelling task : classification, predict next label, naive bayes, HMM, MEEM, CRF
    - features : neighboring words, preceding labels, POS, prefix, suffix, wird shape
  - generative probabilistic model : words (known) $W=(w_1,\ldots,w_n)$, states (unknown) $E=(e_1,\ldots,e_n)$
    - assume text produced : $P(E,W)$
    - model : $\arg\max_E P(E\mid W)=\arg\max_E P(E)P(W\mid E)$
    - transition probabilities : bigram model, $P(E)\approx \Pi_{i=2,\ldots, n} P_E(e_i\mid e_{i-1})$, $P_E(e_i\mid e_{i-1})$ estimated by maximum likelihood
    - word emission probailities : $P(W\mid E)\approx\Pi_{i=1,\ldots,n}P_W(w_i\mid e_i)$, $P_W(w_i\mid e_i)$
  - smoothing : unseen words might only miss in training set, $P_{WS}(w_i\mid e_i)=\lambda P_W(w_i\mid e_i)+(1-\lambda)\frac{1}{n}$
- information extraction : extract structured information from text
  - statement : IS-A, RELATED-TO, DEPENDS-ON, LOCATED-IN
  - typed statements : between types, PART-OF, RELATED-TO, LOCATED-IN 

  - hand-written pattern : "Y such as X ((, X)* (, and|or) X)", NER then "cures(DRUG, DISEASE)", high-precision, tailored to specific domains, human low recall
  - supervised machine learning
    - training set : hard to create, choose relevant NER and relations, use unlabeled entity pairs as negative samples
    - classifier : naives bayes, filtering classifier (detect whether relation exists among entities), relation-specific classifier (detect relation label)
    - features : bag of words, bigrams, headword, stems, types, syntactic features, parse tree
  - bootstrapping : no training data, few high-precision patterns, find entity pairs that match pattern, find sentences containing those, generalize entities and generate new patterns
  - semantic drift : LOC hostels, LOC but geneva not located in lausanne
  - confidence : confirmed set of pairs of mentions $M$, $conf(p)=\frac{hits_p}{finds_p}\log(finds_p)$
    - $hits_p$ : set of tuples in $M$ that new pattern matches
    - $finds_p$ : total set of tuples that new pattern matches
  - distant supervision : no training data, use large corpus to collect training data, classify to predict label from other sources, high precision but low recall, feasible, match only if all individual features match

### Taxonomy induction

- taxonomy induction :  extract related facts from documents (classification of animals), not unique
  - hyponyms : subordinate terms, can inherit properties from hypernyms (more general terms)
  - ISA : no need to learn inferred facts as transitive
  - task : start from root/basic concept
    - learn relevant terms : hypernym/hyponym relationship
    - filter out : erroneous terms and relation
    - induce : taxonomy structure  
      ![](./assets/img/cs423-14.jpg)
  - learning hypernym
    ![](./assets/img/cs423-15.jpg)
  - inducing hypernym graph : many possible relationships among concepts and terms not likely to be discovered
    ![](./assets/img/cs423-16.jpg)
  - cleaning hypernym graph : determine all basic concept (not hypernym of another concept), determine all root concepts (no hypernyms), for each basic root concept pair, select all hypernym paths that connect them, choose longest one for final taxonomy

### Schema integration

- keys task in distributed information management : more data ? more models ? more useful information ? supply vs demand ?, interpretation and different views of data source
- schema matching : integration of heterogeneous data sources
  ![](./assets/img/cs423-16.1.jpg)
  - manual : common practice today
  - tools : based on structural and content features, estiablish correspondences and rank according to quality (error frequent and unavoidable)
  - universe of database : universe $U$ finite set of possible instances
  - similarity of classes : $A$, $B$ classes (subsets of $U$), jaccard $sim(A,B)=\frac{\abs{A\cap B}}{\abs{A\cup B}}=\frac{P(A,B)}{P(A,B)+P(\bar A,B)+P(A,\bar B)}$, does not work when information has structurally different representation
    ![](./assets/img/cs423-16.2.jpg)
  - classes : intension is similar (instance), extension is different, complex features
    - attributes and relationship names
    - structural relationship : data types
    - distribution features : data values
    - content features : text
      ![](./assets/img/cs423-17.jpg)
  - finding corresponding classes : $U1$ (DB1) and $U2$ (DB2), $U1\cap U2=\emptyset$
    - probabilistic approach : give instance $i$ with feature $T_i$ belong to class $A$
      - naives bayes : $P(A\mid T_i)=P(T_i\mid A)P(A)P(d)\propto P(T_i\mid A)P(A)$
      - known : $P(A)=\frac{\abs{A}}{\abs{U_1}}$
      - independence assumption $P(T_i\mid A)=P(t_1\mid A)\cdots P(t_n\mid A)$ 
      - $T_A$ being bag of all terms in all instances of $A$ : $P(t\mid A)=\frac{\abs{t\in T_A}}{\sum_{t'}\abs{t'\in T_A}}$
        ![](./assets/img/cs423-18.jpg)
        ![](./assets/img/cs423-19.jpg)
- node mapping : alternative class mapping
  - naive approach : order matchings by probability, choose most probable matching, produce mapping among the classes, remove mapped classes, choose next most probable matching and repeat, consistency constraints may be violated

### Networked schema integration

- data integration networks : different experts may contribute input on schema matching
- wisdom of network : leverage knowledge from network
  - pay-as-you-go schema matching : generate probabilistic matching network (pSMN), keep all potential mappings and assess probability of correctness
    ![](./assets/img/cs423-19.1.jpg)
  - matching instances : consistency constraints satisfied, drop as little knowledge as possible
    - uniqueness : attribute cannot be mapped to two different attributes
    - transitivity
    - maximal : minimal number of claimed correspondences removed
    - probability of correctness $p_c$ with correspondence $c$ : $p_c=\frac{\text{#number of matching instances that contain c}}{\text{#total number of matching instances}}$, high complexity computation, use non-uniform sampling for efficient approximation
      ![](./assets/img/cs423-19.2.jpg)
  - reducing network uncertainty : $H( C)=-\sum_{k\in C}p_c\log p_c+(1-p_c)\log(1-p_c)$ with information gain $IG( C)=H( C)-H(C\mid c)$
  - selecting best matching instance : after obtaining user feedback, find matching instance with minimal repair distance (dropping as few correspondence as possible), maximal likelihood ($\Pi_{c\in C}p_c$)
    ![](./assets/img/cs423-20.jpg)
