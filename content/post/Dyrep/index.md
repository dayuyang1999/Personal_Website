---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Dynamic Graph: Dyrep"
subtitle: "Temporal Process, 2-scale of interaction."
summary: ""
authors: 
- Dylan Yang
tags: 
- Graph Neural Network
categories: []
date: 2021-09-17T20:24:36-04:00
lastmod: 2021-09-17T20:24:36-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

Published in ICLR 2019. 


# Problem Formulation

**Graph:**

$G_{t}=\left(V_{t}, E_{t}\right)$ denote graph $G$ at time $t$. 
- $G_{t_{0}}=\left(V_{t_{0}}, E_{t_{0}}\right)$ be the initial snapshot of a graph at time $t_{0}$
  - it could be empty or contains graph structure


- $V_{t}$ is the set of nodes 
- and $E_{t}$ is the set of edges (undirected)
  - Persistent edge ONLY appear after topological events




**Event**

2 kinds of events:
- communication 
- association

$e=(u, v, t, k)$
- $k \in\{0,1\}$ 
  -  $k=0$ is topological evolution (association)
  -  $k=1$ is interaction (communication)


Complete sequence of observed events in window $[0, T]$ as: $O=\left\{(u, v, t, k)_{p}\right\}_{p=1}^{P}$.


<br><br>
<br><br>

# Model: DyRep

## In general

DyRep is a inductive method(like GraphSAGE) instaed of transductive(like word2vec). So it can generate for unseen data.

Two-time scale point process; Intensity is parameterized by an inductive representation network


{{% callout note %}}

Some implicit Assumptions of DyRep:
- At any time $t$, the occurrence of an event, from either of these processes, is ONLY dependent on the most recent state of the graph.
  - **may be generalized!!!**

- Intensity must be positive
- v's embedding is not depends on v itself's neihgbors


{{% /callout %}}

<br><br>

## Modeling two-time sclae graph dynamics

{{% callout note %}}

communication and association is called "two-time scale" in this paper.

{{% /callout %}}

intensity function $\lambda_{k}^{u, v}(t)$:

$$
\lambda_{k}^{u, v}(t)=f_{k}\left(g_{k}^{u, v}(\bar{t})\right)
$$
- $\bar{t}$ = the timepoint just before current event
- $g_{k}(\bar{t})$ computes the compatibility of two nodes, $\mathbf{z}^{u}(\bar{t})$ and $\mathbf{z}^{v}(\bar{t})$:

$$
g_{k}^{u, v}(\bar{t})=\boldsymbol{\omega}_{k}^{T} \cdot\left[\mathbf{z}^{u}(\bar{t}) ; \mathbf{z}^{v}(\bar{t})\right]
$$


The choice of $f_k$ must fit two criteria: 
- intensity need to be positive
- has k specialized parameter(give flexibility for 2 types of timescale)


Here is author's choice, a softplus function:

$$
f_{k}(x)=\psi_{k} \log \left(1+\exp \left(x / \psi_{k}\right)\right)
$$
- $x=g(\bar{t})$ in our case and $\psi_{k}(>0)$ is scalar time-scale parameter learned as part of training

<br><br>

## Inductive representation network

to compute $g$, we need node representations.

The node representation is updated though 3 priciples:

$$
\mathbf{z}^{v}\left(t_{p}\right)=\sigma(\underbrace{\mathbf{W}^{\text {struct }} \mathbf{h}_{\text {struct }}^{u}\left(\overline{t_{p}}\right)}_{\text {Localized Embedding Propagation }}+\underbrace{\mathbf{W}^{r e c} \mathbf{z}^{v}\left(\overline{t_{p}^{v}}\right)}_{\text {Self-Propagation }}+\underbrace{\mathbf{W}^{t}\left(t_{p}-\overline{t_{p}^{v}}\right)}_{\text {Exogenous Drive }}),
$$
- Local Neighbor 
  - $\mathbf{h}_{\text {struct }}^{u} \in \mathbb{R}^{d}$ is the output of aggregator function on u's neighbors.
- Self-Propagation
- Time Switch(Exogenous Drive)
  - the logic is same as Time2Vec



{{% callout note %}}

There's no justification that why the embedding is depends and only depends on those 3 factors. 

{{% /callout %}}

{{% callout note %}}

The formulation is inductive.(Think about why it's inductive, if giving a new unseen node...)

{{% /callout %}}

### Localized Embedding Propagation

![](https://cdn.mathpix.com/snip/images/v4et_upSIanbQ6CyDDhCEnkUSehvNBlbKerMgtwUAlA.original.fullsize.png)

- under each interaction, the information is passed from neighbors of node u to node v and neighbors of node v to node u.
  
{{% callout note %}}

If u,v is not associated before, but has a communication. The node embedding of u, v will still update, but A will not update. (the strucutre of the net will not change, and u,v are still not neihghbor for each other).
- the only differences btw k=0 or k=1 is if we need to update A...

{{% /callout %}}









<br><br>

## Aggregator (analogue to Attention mechanism(S) + update A)

{{% callout note %}}

The following implicit assumption embedded in the design of aggregator:
- the update value is defined by author, quite subjective, and has no flexibility.(Specifically, the main idea of Algorithm 1 is: "if you have a lot neighbor, then the important of each neighbor is relatively smaller".)
-  $k=0, A=1$ cannot happen (if u and v associated before, the association event cannot happen btw them again)

{{% /callout %}}

{{% callout warning %}}


I think There is an error:
- $\lambda_{k}^{i^{j}}(t)$ is not yet computed...why it shows in the algorithm to compute itself...

{{% /callout %}}


when encounter an event $(u, v, t, k)$, the aggregator will:
- give $\mathbf{h}_{\text {struct }}^{u} \in \mathbb{R}^{d}$
-  update A and S from $\mathbf{A}(\bar{t})$ and $\mathcal{S}(\bar{t})$. (Output: $\mathbf{A}(t)$ and $\mathcal{S}(t)$)
   -  A: adjacency matrix
   -  S: strengh of connection matrix

![](https://cdn.mathpix.com/snip/images/MR2JYOYnzP36Z2PyIz2SovQfq9-0347FOdPmUbfRPdA.original.fullsize.png)


Use S to compute attention:

$$
q_{u i}(t)=\frac{\exp \left(\mathcal{S}_{w i}(t)\right)}{\sum_{i^{\prime} \in \mathcal{N}_{u}(t)} \exp \left(S_{u i^{\prime}}(t)\right)}
$$

and aggregator (here the authro use max)

$$
\mathbf{h}_{\text {struct }}^{u}(\bar{t})=\max \left(\left\{\sigma\left(q_{u i}(t) \cdot \mathbf{h}^{i}(\bar{t})\right), \forall i \in \mathcal{N}_{u}(\bar{t})\right\}\right)
$$





<br><br>
<br><br>


# Training 

## Parameters:

$\boldsymbol{\Omega}=\left\{\mathbf{W}^{\text {struct }}, \mathbf{W}^{r e c}, \mathbf{W}^{t}, \mathbf{W}^{h}, \mathbf{b}^{h},\right. \left.\left\{\boldsymbol{\omega}_{k}\right\}_{k=0,1},\left\{\psi_{k}\right\}_{k=0,1}\right\}$

## Method: MLE

### Likelihood function

Here the target is to min the negative log likelihodd:

$$
\mathcal{L}=-\sum_{p=1}^{P} \log \left(\lambda_{p}(t)\right)+\int_{0}^{T} \Lambda(\tau) d \tau
$$
- $\lambda_{p}(t)=\lambda_{k_{p}}^{u_{p}, v_{p}}(t)$
- $\Lambda(\tau)=\sum_{u=1}^{n} \sum_{v=1}^{n} \sum_{k \in\{0.1\}} \lambda_{k}^{u, v}(\tau)$

{{% callout note %}}

The 2nd part of the likelihood function is impossible to solve

1. the integral is unanalysisable
2. the $\Lambda$ is expensive ($O(n^2k)$)

{{% /callout %}}


Solution:
1. No research about hawkes process actually compute the integral
   1. they all locally optimize $\mathcal{L}$, using Monte-Carlo Integration
      1. sampling non-events instead of considering all pairs of non-events.


### batch training

the Backpropagation Through Time (BPTT) training is conducted over a global sequence; for maintain the dependencies between events across
sequences while avoiding gradient related issues.


<br><br>
<br><br>

# evaluation

## Link prediction

prediction task: given node v, given time t, given event type k, which is the most likely node u.

The density function is 

$$
f_{k}^{u, v}(t)=\lambda_{k}^{u, v}(t) \cdot \exp \left(\int_{\bar{t}}^{t} \lambda(s) d s\right)
$$

---
assume $u,v,k,t$ are all given(t is sometime in the future), I am trying to compute the value of density function.

then I need $\lambda_{k}^{u, v}(t)$, according to (1), since the $f_k$ is a inductive function, so the only thing is $g_{k}^{u, v}(\bar{t})$. 

and then...need node embedding of two candidate nodes u and v.

---

replacing v with all the entities and selected the top ranks, compute Mean Average Rank (MAR) and HITS(@10).


{{% callout note %}}

This link prediction task is quite differnt from what we encounter in the real world. Since you need to provide u,t,k.

In reality, people did not give any kind of info, but it's still computatble.

For example, I want to know in future time t, which pair (u ,v) will have a k event. I can compute all the density for every u and every v...

{{% /callout %}}


## Event time prediction

this is equavalent to "computing the expectation of t" (Neural hawkes process also provides similar predictive task for evaluation).
- you can have interval estimation of the t.

$$
E(t)=\int_{t}^{\infty} t f_{k}^{u, v}(t) d t
$$



- See all appendix H, G
- Dyrep only use hawkes process structure benefitial to training... not timing....








# Appendix

## Summary of Overall Embedding Update Process

1. initial net structure and init node embedding
2. obverve an event $(u,v,k,t)$,
   1. update node embedding of u,v using Eq 4 (Localized Embedding Propagation) 
      1. gathering info of neighbors of the opposite side


## Attention Mechanism

![](https://cdn.mathpix.com/snip/images/wgpL8MXCG6q5NHJZklN7tpzdq24EC87jaz3v67qOKlk.original.fullsize.png)

- aggregator: max
- attention weight $q$: compute using $S$

## Node Attributes and Edge Types Support

initalized with features:
- the first time, $z^{v}\left(\bar{t}_{p}^{v}\right)=\mathbf{x}_{v}$ where $\mathbf{x}_{v}$ is randomly initialized.

updating features:
- adding extra term in Eq. 4

adding edge type info:
- in aggregator, could also add with edge type info




## New nodes

generally, encoder is an inductive function. So it could be generate to a new node.

So, let's say, encounter a new event $(u,v,k,t)$, A and S?

both $\mathbf{A}$ and $\mathcal{S}$ are qualified by time and hence the matrices get updated every time

dimension of the two matrices can be specified in two ways: 
1. Construct both matrices of dimension = total possible no. of nodes in dataset and make the rows belonging to unseen nodes 0. 
2.  Expand the dimensions of matrices as you start seeing new nodes. While we implement the first case, 

2 is better but the author use 1.



## Monte Carlo Estimation for survival term in Likelihood

![](https://cdn.mathpix.com/snip/images/-ktgihHR3i52mfToIvGENbcQrDgEU5yTjiwcdETCKxM.original.fullsize.png)

uniformly pick up $N$ negative sample pair:
- for true u, randomly sample $\tilde{v}$
- for true v, randomly sample $\tilde{u}$


{{% callout warning %}}

I think there's an error, why finally **divide N** is you want to estimate a triple-layer **summation**?

And did not tell anything about the integral over the triple-layer summation ...

{{% /callout %}}



<br><br>
<br><br>

# Some random thinking 

reversely message passing?
- few data: so more node could be updated
- the event influence the neighbors...

Dyrep (Localized Embedding Propagation):
- know the env of target very well

Or asymmetric message passing?
- predator: gathering the neighbor of prey(pick up the best one)
- prey: spread the info ...
  - What define myself? my neighbors? or my opponents' neighbors