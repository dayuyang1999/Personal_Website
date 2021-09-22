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



# Problem Formulation

**Graph:**

$\mathcal{G}_{t}=\left(\mathcal{V}_{t}, \mathcal{E}_{t}\right)$ denote graph $\mathcal{G}$ at time $t$. 
- $\mathcal{G}_{t_{0}}=\left(\mathcal{V}_{t_{0}}, \mathcal{E}_{t_{0}}\right)$ be the initial snapshot of a graph at time $t_{0}$
  - it could be empty or contains graph structure


- $\mathcal{V}_{t}$ is the set of nodes 
- and $\mathcal{E}_{t}$ is the set of edges (undirected)
  - Persistent edge ONLY appear after topological events




**Event**

2 kinds of events:
- communication 
- association

$e=(u, v, t, k)$
- $k \in\{0,1\}$ 
  -  $k=0$ is topological evolution (association)
  -  $k=1$ is interaction (communication)


Complete sequence of observed events in window $[0, T]$ as: $\mathcal{O}=\left\{(u, v, t, k)_{p}\right\}_{p=1}^{P}$.




# Model: DyRep

## In general

DyRep is a inductive method(like GraphSAGE) instaed of transductive(like word2vec). So it can generate for unseen data.

Two-time scale point process; Intensity is parameterized by an inductive representation network


{{% callout warning %}}

Some implicit Assumptions of DyRep:
- At any time $t$, the occurrence of an event, from either of these processes, is ONLY dependent on the most recent state of the graph.
  - **may be generalized!!!**

- Intensity must be positive
- v's embedding is not depends on v itself's neihgbors


{{% /callout %}}



## Modeling two-time sclae graph dynamics

{{% callout warning %}}

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



{{% callout warning %}}

There's no justification that why the embedding is depends and only depends on those 3 factors. 

{{% /callout %}}

{{% callout warning %}}

The formulation is inductive.(Think about why it's inductive, if giving a new unseen node...)

{{% /callout %}}


## Aggregator

{{% callout warning %}}

The following implicit assumption embedded in the design of aggregator:
- the update value is defined by author, quite subjective, and has no flexibility.
-  $k=0, A=1$ cannot happen (if u and v associated before, the association event cannot happen btw them again)

AND There may be a typo:
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

{{% callout warning %}}

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


# evaluation

## Link prediction




## event time prediction






See all appendix H, G








