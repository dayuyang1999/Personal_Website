---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "MTPP via Graph used for NRI"
subtitle: "Neural Relation Inference for Multi-dimensional Temporal Point Processes via Message Passing Graph"
summary: ""
authors: []
tags: []
categories: []
date: 2021-09-24T17:00:31-04:00
lastmod: 2021-09-24T17:00:31-04:00
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

Zhang, Yunhao, and Junchi Yan. 2021. “Neural Relation Inference for Multi-Dimensional Temporal Point Processes via Message Passing Graph.” In , 3:3406–12. https://doi.org/10.24963/ijcai.2021/469.


---


<br><br>
<br><br>

# Problem setting

Neural Relation Inference(NRI) describe a problem that:

There's a directed graph, the node embedding is keeping updating. However, you have no idea the influence relationship between nodes.

---

For example, 

we have 5 balls, each pair of balls may connect with a spring. Then we press all springs and release this system. The ball will bounce in the 3d space. Then, the node embedding could be their location coordicators (a 3-d dimension). 

Then, the task is: By obversing a sequence of balls' locations, could we predict which pair of balls are connected?

---

For this paper:

- $D$: number of observing sequences; $i_{n}^{m} \in\{1, \ldots, D\}$ 
  - each node has one sequence, so $\mathbf{A} \in\{0,1\}^{D \times D}$
- $N_m$: Length of each sequence; $S_{m}=\left\{\left(t_{n}^{m}, i_{n}^{m}\right)\right\}_{n=1}^{N_{m}}$
- $M$:  types of events; $S=\left\{S_{m}\right\}_{m=1}^{M}$

**Multi-dim TPP: $Multi$ refers to $D$ (we seperately model every node)**



<br><br>
<br><br>

# Model Framework and Overall Objective


Given event sequence $S$, their latent relation matrix $A$. 

Given prior $p(A)$, use MTPP to model $p_\theta(S|A)$, then the goal is to maximum the likelihood that obversing $S$. (maximum $p_\theta(S)$).

By maximum $p_\theta(S)$, could get the posterior $p_\theta(A|S)$.
- posterior has no analytical solution. Using Variational Inference (VI) for approxiamation.
- minimize KL divergence between $q_{\phi}({A})$ and $p_{\theta}({A} \mid S)$

So, the overall objective is (to maximum):

$$\mathcal{L}(\theta, \phi ; S)=-\operatorname{KL}\left[q_{\phi}(\mathbf{A}) \| p_{\theta}(\mathbf{A} \mid S)\right]+\log p_{\theta}(S)$$

{{% callout note %}}

This paper infer a general Bayesian Training Framework:
(Tons of ML papers use training process like this, such as (Knyazev, Augusta, and Taylor 2020)...)

What I know: prior info(may be a multi-nominal distribution, some ratios); obverved sequence.

1. Given a prior
2. modeling conditional distrib (where the fancy staff located)
3. compute the posterior

4. restrict the distance btw prior and post cannot be too far away(an assumption)
5. maximum the likelihood of observing sequence
6. minimize the likelihood of not obversing something (optional)




{{% /callout %}}

For this problem, Specific prior can be introduced based on domain knowledge: e.g. setting $p\left(A_{j i}=1\right)=0.2$ encourages the learned posterior to be sparse.


<br><br>
<br><br>

# Conditional Distri Modeling
Via  MTPP and GNN.

{{% callout note %}}
Do not necessarily arise a graph when talking about GNN.

The correct steps are:

1. "My neighbors influence me", or "My neighbors define who I am"
2. msg passing mechanism
3. GNN
4. Adjacency Matrix
5. Graph

{{% /callout %}}


**Node embedding is $h$:**

historical info at time $t_i$ of a sequence: RNN, hidden state $h_i$, ${h}_{i}(t) \in \mathbb{R}^{d}$
- So at $t \in [0, T)$, we have a set of $h$, $H$. $\mathbf{H}(t)=\left\{\mathbf{h}_{i}(t)\right\}_{i=1}^{D}$
  
**Graph Structure $A$, directed.**

**Update of $h$**

everytime an event $\left(t_{n}, i_{n}\right)$ occurs, $\mathbf{h}(t)$ will be updated by 3 factors:

1. External Drive
   1. time switch term, for a constant update process
2. Self-Update
3. Affected-Update

<br><br>

---

**External Drive**

$\mathbf{h}_{i}\left(t_{n}^{-}\right)=f_{\text {Ext }}\left(\mathbf{h}_{i}\left(t_{n-1}\right), \Delta t_{n}\right)$

**Self update**

$\mathbf{h}_{i_{n}}\left(t_{n}\right)=\mathbf{G R U}_{\text {Self }}\left(t_{n}, \mathbf{h}_{i_{n}}\left(t_{n}^{-}\right)\right)$
- a new input, a old $h$...


**affected update**

event in dimension $i_n$ can effect the hidden state of other dimensions $j \neq i_{n}$


$\operatorname{MSG}_{\left(i_{n}, j\right)}\left(t_{n}\right)=A_{j i_{n}} f_{M S G}\left(\left[\mathbf{h}_{i_{n}}\left(t_{n}\right), \mathbf{h}_{j}\left(t_{n}^{-}\right)\right]\right)$
- first update myself, and then update "others" via msg passing
- "others" follow same RNN logic...update their $h$
$\mathbf{h}_{j}\left(t_{n}\right)=\operatorname{GRU}_{A f f e c t}\left(\operatorname{MSG}_{\left(i_{n}, j\right)}\left(t_{n}\right), \mathbf{h}_{j}\left(t_{n}^{-}\right)\right)$
---

<br><br>


Use $h_i(t_n)$ to formulate CIF: 

$$
\lambda_{i}(t)=\exp \left(\mathbf{v}^{\top} \cdot \mathbf{h}_{i}\left(t_{n}\right)+w_{i}\left(t-t_{n}\right)+b_{i}\right)
$$

Notice that there are 3 substripts: $D, m, N_m$.

{{% callout note %}}

when an event happens, trigger itself update once. And it influences everyone else (then everyone else update as well).

during the waiting period, time is modeled outside $h$ to allow analytical analysis.

{{% /callout %}}




![](https://cdn.mathpix.com/snip/images/WoVmZJHHoPFmkiqjG4IegD-gckLj3jhFYu0XoGreNEs.original.fullsize.png)

<br><br>
<br><br>


# Training

Given CIF $\lambda$, the log likelihood $\log p_{\theta}\left(S_{m} \mid \mathbf{A}\right)$ of the observing sequence $S=\left\{S_{m}\right\}_{m=1}^{M}$. for $M$ number of $p_{\theta}\left(S_{m} \mid \mathbf{A}\right)$, Assume they are conditional independent with each other.

So simply product the likelihood for each node together, and for each node, it's a point process:



$$
\begin{aligned}
& \log p_{\theta}(S \mid \mathbf{A}) = \sum_{m=1}^{M}\left(\sum_{n=1}^{N_{m}} \log \lambda_{i_{n}^{m}}^{(m)}\left(t_{n}^{m}\right)-\sum_{i=1}^{D} \int_{0}^{T} \lambda_{i}^{(m)}(t) d t\right)
\end{aligned}
$$

- for each type ($\sum^M$)
- $D$ number of nodes


---
If we only has one type of interaction:

$$\mathcal{L}(s)=\left[\Pi_{n=1}^{N} \lambda_{i_{n}}^{*}\left(t_{n}\right)\right] \Pi_{i=1}^{D} \exp \left(-\int_{0}^{T} \lambda_{i}^{*}(t) d t\right)$$



---

It is straightforward to compute L and use gradient descent to optimize. However, the expectation of $\log p_{\theta}(S \mid \mathbf{A})$ has no analytical solution.
-  use Gumbel-Softmax sampling which approximates discrete distributions with continuous ones to provide differ- entiable samples.
- and the $L$ is actually use ELBO

![](https://cdn.mathpix.com/snip/images/hlbFdP1ETU-UrYI7_puZZ8l10tT_KYkqRjNypsj35N0.original.fullsize.png)

