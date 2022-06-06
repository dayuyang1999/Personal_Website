---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "TGN"
subtitle: ""
summary: "A basic framework of continous temporal dynamic graph modeling. Benchmarked in 2020"
authors:
- Dylan Yang
tags: 
- Graph Neural Network
categories: []
date: 2021-09-21T19:06:38-04:00
lastmod: 2021-09-21T19:06:38-04:00
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

Clarification:
- Summarize from Rossi's [Github Repo](https://github.com/twitter-research/tgn)



---


# Problem Formulation


a sequence of time-stamped events: 

![](https://cdn.mathpix.com/snip/images/5F8v6OCmL6Uyyovb9qNc_z9cf7MfWMbyKXPU0jXB30s.original.fullsize.png)

## Node event $\mathbf{v}_{i}(t)$
 1. $i$: index
 2. $v$: vector attribute of the event

if $i$ never seen before, create node $i$ with given feature $v$

## interaction 

a directed temporal edge $\mathbf{e}_{i j}(t)$ (could be more than one edge btw pair of nodes)


# Modeling: Temporal Graph Net

## Modules

### Memory


$\mathbf{s}_{i}(t)$: The memory (state) of the model at time t for each node i the model has seen so far.
- The memory of a node is updated after an event
- represent the node’s history in a compressed format.


### Message 

For each event involving node $i$. a message is computed to undate $i$ 's memory.


#### Interaction message 
an interaction $\mathbf{e}_{i j}(t)$ happened (btw $i$ and $j$):

message is computed:

$$\mathbf{m}_{i}(t)=\mathrm{msg}_{\mathrm{s}}\left(\mathbf{s}_{i}\left(t^{-}\right), \mathbf{s}_{j}\left(t^{-}\right), \Delta t, \mathbf{e}_{i j}(t)\right)$$

$$\mathbf{m}_{j}(t)=\operatorname{msg}_{\mathrm{d}}\left(\mathbf{s}_{j}\left(t^{-}\right), \mathbf{s}_{i}\left(t^{-}\right), \Delta t, \mathbf{e}_{i j}(t)\right)$$

#### node-event message


$$\mathbf{m}_{i}(t)=\mathrm{msg}_{\mathrm{n}}\left(\mathbf{s}_{i}\left(t^{-}\right), t, \mathbf{v}_{i}(t)\right)$$
- only computed for the node involved in the event

### message aggregattor


in the same batch, a single node $i$ may encounter several updates, which cause troble.

Develop a mechanism to aggregate message $\mathbf{m}_{i}\left(t_{1}\right), \ldots, \mathbf{m}_{i}\left(t_{b}\right)$ for $t_{1}, \ldots, t_{b} \leq t$.

$$\overline{\mathbf{m}}_{i}(t)=\operatorname{agg}\left(\mathbf{m}_{i}\left(t_{1}\right), \ldots, \mathbf{m}_{i}\left(t_{b}\right)\right)$$
- RNN, attention... many possible choice; here use `most recent message` (keep only most recent message for a given node) and `mean message` (average all messages for a given node).

### Memory Updator

if an event involve $i$, memory should be updated:


$$\mathbf{s}_{i}(t)=\operatorname{mem}\left(\overline{\mathbf{m}}_{i}(t), \mathbf{s}_{i}\left(t^{-}\right)\right)$$


#### After Interaction

#### After node-wise events

mem is a learnable memory update function; LSTM or GRU.

### Embedding

$$\mathbf{z}_{i}(t)=\operatorname{emb}(i, t)=\sum_{j \in n_{i}^{k}([0, t])} h\left(\mathbf{s}_{i}(t), \mathbf{s}_{j}(t), \mathbf{e}_{i j}, \mathbf{v}_{i}(t), \mathbf{v}_{j}(t)\right)$$

#### Temporal Graph Attention(one possible encoder)

$$\begin{aligned} \mathbf{h}_{i}^{(l)}(t) &=\operatorname{MLP}^{(l)}\left(\mathbf{h}_{i}^{(l-1)}(t) \| \tilde{\mathbf{h}}_{i}^{(l)}(t)\right) \\ \tilde{\mathbf{h}}_{i}^{(l)}(t) &=\text { MultiHeadAttention }^{(l)}\left(\mathbf{q}^{(l)}(t), \mathbf{K}^{(l)}(t), \mathbf{V}^{(l)}(t)\right) \\ \mathbf{q}^{(l)}(t) &=\mathbf{h}_{i}^{(l-1)}(t) \| \boldsymbol{\phi}(0) \\ \mathbf{K}^{(l)}(t) &=\mathbf{V}^{(l)}(t)=\mathbf{C}^{(l)}(t) \\ \mathbf{C}^{(l)}(t) &=\left[\mathbf{h}_{1}^{(l-1)}(t)\left\|\mathbf{e}_{i 1}\left(t_{1}\right)\right\| \boldsymbol{\phi}\left(t-t_{1}\right), \ldots, \mathbf{h}_{N}^{(l-1)}(t)\left\|\mathbf{e}_{i N}\left(t_{N}\right)\right\| \boldsymbol{\phi}\left(t-t_{N}\right)\right] \end{aligned}$$

- read from bottom to top
- input: 
  - $i$ 's representation $\mathbf{h}_{i}^{(l-1)}(t)$
  - neighbor's representation $\left\{\mathbf{h}_{1}^{(l-1)}(t), \ldots, \mathbf{h}_{N}^{(l-1)}(t)\right\}$
  - time stamp $t_{1}, \ldots, t_{N}$
  - features $\mathbf{e}_{i 1}\left(t_{1}\right), \ldots, \mathbf{e}_{i N}\left(t_{N}\right)$ , which is the interaction when form the edge

#### Temporal Graph Sum(Another possible encoder)

$$
\begin{aligned}
\mathbf{h}_{i}^{(l)}(t) &=\mathbf{W}_{2}^{(l)}\left(\mathbf{h}_{i}^{(l-1)}(t) \| \tilde{\mathbf{h}}_{i}^{(l)}(t)\right) \\
\tilde{\mathbf{h}}_{i}^{(l)}(t) &=\operatorname{ReLu}\left(\sum_{j \in n_{i}([0, t])} \mathbf{W}_{1}^{(l)}\left(\mathbf{h}_{j}^{(l-1)}(t)\left\|\mathbf{e}_{i j}\right\| \boldsymbol{\phi}\left(t-t_{j}\right)\right)\right)
\end{aligned}
$$



{{% callout note %}}


About time encoding $\phi$: 

here $\phi$ is a vector. 

Time is a important feature but hard to be compatible in a lot of Deep Learning Architecture.

Here is a method to apply a model-agnostic vector representation for time, called Time2Vec, that can be easily imported into many
existing and future architectures and improve their performances.

detail see [Github](https://github.com/dayuyang1999/Time2Vec-PyTorch)


{{% /callout %}}




