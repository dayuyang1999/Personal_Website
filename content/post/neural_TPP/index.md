---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Neural Temporal Point Process, A Review"
subtitle: ""
summary: ""
authors: 
- Dylan Yang
tags:
- Point Process
- Statistics
categories: []
date: 2021-09-23T19:25:50-04:00
lastmod: 2021-09-23T19:25:50-04:00
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

Shchur, Oleksandr, Ali Caner Türkmen, Tim Januschowski, and Stephan Günnemann. 2021. “Neural Temporal Point Processes: A Review.” ArXiv:2104.03528 [Cs], August. http://arxiv.org/abs/2104.03528.


---

Neural TPP = TPP + Deep Learning

Model Sequence Events: any sequential model(RNN, transfomer) + TPP



---

Two kinds of model(differentiate by how to update $h$)
- $h$ is the "history info summation term"

1. $h$ is update discretely (unrelated to $t$)
2. $h$ is update via time (a function of $t$)

**small change will lead big difference for training and prediction (since we have 2 integral term over $t$)**




# Autoregressive Neural TPP

e.g.: [Du et al., 2016].

---

3 steps:

1. Represent each event $\left(t_{j}, m_{j}\right)$ as a feature vector $\boldsymbol{y}_{j}$
2. Encode the history $\mathcal{H}_{t_{i}}$ (represented by a sequence of feature vectors $\left.\left(\boldsymbol{y}_{1}, \ldots, \boldsymbol{y}_{i-1}\right)\right)$ into a fixed-dimensional history embedding $\boldsymbol{h}_{i}$.
3. Use the history embedding $\boldsymbol{h}_{i}$ to parametrize the conditional distribution over the next event $P_{i}\left(t_{i}, m_{i} \mid \mathcal{H}_{t_{i}}\right)$


## Representing Events as Feature Vectors

A event $(t_i, k_i)$ has two part of info:
- timing
- marker


## Encoding the History into a Vector

### RNN encoder

**advantage**

RNN is computational-resources-saving even comparted to Hawkes Process.

$O(n)$ vs $O(n^2)$

**Downside**

Since RNN is sequential model, it' chanllenging to apply parallel training. So such models are usually trained via truncated backpropagation through time, which only provides an approximation to the true gradients

### aggregation encoder

encoder is **independently** encoding each of the event and then aggregate those embeddings together.

such as: transfomer (multi-head attention)

## Parameterize $\lambda$

here $\tau$ is used for represrenting:  the next inter-event time

---
Some terms:
1. probability density function $f_{i}^{*}\left(\tau_{i}\right)$
2. cumulative distribution function $F_{i}^{*}\left(\tau_{i}\right)=\int_{0}^{\tau_{i}} f_{i}^{*}(u) d$
3. survival function $S_{i}^{*}\left(\tau_{i}\right)=1-F_{i}^{*}\left(\tau_{i}\right)$
4. hazard function, or CIF $\lambda$, $\phi_{i}^{*}\left(\tau_{i}\right)=f_{i}^{*}\left(\tau_{i}\right) / S_{i}^{*}\left(\tau_{i}\right)$
5. cumulative hazard function $\Phi_{i}^{*}\left(\tau_{i}\right)=\int_{0}^{\tau_{i}} \phi_{i}^{*}(u) d u$.


CIF:

$\lambda^{*}(t)=\left\{\begin{array}{ll}\phi_{1}^{*}(t) & \text { if } 0 \leq t \leq t_{1} \\ \phi_{2}^{*}\left(t-t_{1}\right) & \text { if } t_{1}<t \leq t_{2} \\ \vdots & \\ \phi_{N+1}^{*}\left(t-t_{N}\right) & \text { if } t_{N}<t \leq T\end{array}\right.$

---

pdf:
$$
f_{i}^{*}\left(\tau_{i}\right)=f\left(\tau_{i} \mid \boldsymbol{\theta}_{i}\right), \quad \text { where } \quad \boldsymbol{\theta}_{i}=\sigma\left(\boldsymbol{W} \boldsymbol{h}_{i}+\boldsymbol{b}\right)
$$

---

Here are some contraints for parametric functions:
1. $\tau_i$ is strictly positive (so on pdf: $\int_{-\infty}^{0} f_{i}^{*}(u) d u=0$)
1. TPP is non-stopping and the next event will eventually arrive
   1. on pdf: $\int_{0}^{\infty} f_{i}^{*}(u) d u=1$

{{% callout note %}}

Choice of CIF may effect if $\lambda$ can be integrated analytically.

A common choice follows (Zuo et al., 2020):
![](https://cdn.mathpix.com/snip/images/TNYaNUe6-emIm9jTT6fWN4IS_xIOdugG6oUANhpP3LU.original.fullsize.png)

and $f_k$ is a softpus: $f_{k}(x)=\beta_{k} \log \left(1+\exp \left(x / \beta_{k}\right)\right)$

{{% /callout %}}


When parameterize $\lambda$, keep in mind the folowing aspects:

1. Flexibility

- if the corresponding pdf allow us to approxiate any distribution?


2. Closed-form likelihood

- Could compute cdf, pdf, analytically

3. sampling

best case: inversion sampling

bad case: thinning sampling (do not benefit from parallel hardware like GPUs)

---

## Modeling Marks

the joint distribution of an event is factorized by the distribution $P_{i}^{*}\left(\tau_{i}, m_{i}\right)$. if we assume the independent of two:

$$P_i^*(\tau_i, m_i)=P_i^*(\tau_i) \cdot P_i^*(m_i)$$

these two are both parameterzed using $h_i$ via $\lambda$:

$$\lambda_{k}^{*}(t)=p_{i}^{*}\left(m_{i}=k\right) \cdot \lambda_{i}^{*}\left(t-t_{i-1}\right)$$


Some times, independence assumption is too strong for the problem. mark could conditioned on time, or time could conditioned on mark.


## conditioned between mark and time


Must specify k seperate distribution $P_{i}^{*}\left(\tau_{i} \mid m_{i}=k\right)$ for each mark $k \in\{1, \ldots, K\}$

### time conditoned on mark

for each $\tau$, specify a conditioned distribution for mark $P^{*}\left(m_{i} \mid \tau_{i}=\tau\right)$.

$$\lambda_{k}^{*}(t)=p_{i}^{*}\left(m_{i}=k \mid \tau_{i}=t-t_{i-1}\right) \cdot \lambda_{i}^{*}\left(t-t_{i-1}\right)$$


<br><br>
<br><br>

# Continuous-time State Evolution

e.g.: [Mei and Eisner, 2017] 

---

Mainly two process

$$\begin{aligned} \boldsymbol{h}\left(t_{i}\right) &=\operatorname{Evolve}\left(\boldsymbol{h}\left(t_{i-1}\right), t_{i-1}, t_{i}\right) \\ \lim _{\varepsilon \rightarrow 0} \boldsymbol{h}\left(t_{i}+\varepsilon\right) &=\operatorname{Update}\left(\boldsymbol{h}\left(t_{i}\right), \boldsymbol{y}_{i}\right) \end{aligned}$$
- Evolve constantly evolve $h$ during the waiting period $(t_{i−1} , t_i ]$
  - one choice is exponental decay, like eq 7 in [Mei and Eisner, 2017].
- when the event happens, the $h$ was updated 



# Compare

## In a continuous-time model:

the state $h(t)$ is used to directly define the intensity $λ^*_k$ for each mark k as:

$$\lambda_{k}^{*}(t)=g_{k}(\boldsymbol{h}(t))$$
- $g_k$ is a non-linear function mapping $h$ to $\lambda$. softplus is usually used

## In autoregressive model:

the intensity $λ^*_k$ is defined through 3 factors (commonly used):

- $h$, which update only when event happen
- $t-t_j$, time switch factor, which update constantly with $t$
- a base rate

Here are 2 examples from (Du et al. 2016) and (Zuo et al., 2020.):

$$\lambda^{*}(t)=\exp (\underbrace{\boldsymbol{v}^{\pm}{ }^{\top} \cdot \boldsymbol{h}_{j}}_{\begin{array}{c}\text { past } \\ \text { influence }\end{array}}+\underbrace{w^{t}\left(t-t_{j}\right)}_{\begin{array}{c}\text { current } \\ \text { influence }\end{array}}+\underbrace{b^{t}}_{\begin{array}{c}\text { base } \\ \text { intensity }\end{array}})$$


---

$$\lambda_{k}\left(t \mid \mathcal{H}_{t}\right)=f_{k}(\underbrace{\alpha_{k} \frac{t-t_{j}}{t_{j}}}_{\text {current }}+\underbrace{\mathbf{w}_{k}^{\top} \mathbf{h}\left(t_{j}\right)}_{\text {history }}+\underbrace{b_{k}}_{\text {base }})$$
- $f_k$ is a softplus







## Adv and Cost


Advantages of continous-time compared with autoregression
1. ...



Cost:
1. likelihood  (training)
2. prediction (sampling)

requires numerically approximating intractable integrals.


# Parameter Estimation

## MLE

Negative log likelihood:

(A "signle sequence with marks" Case)

$$
\begin{aligned}
-\log p(X)=&-\sum_{i=1}^{N} \left[ \sum_{k=1}^{K} \mathbb{1}\left(m_{i}=k\right) \log \lambda_{k}^{*}\left(t_{i}\right) +\sum_{k=1}^{K}\left(\int_{0}^{T} \lambda_{k}^{*}(u) d u\right) \right]
\end{aligned}
$$

Interpretation:

the probability of obverving an event of type $k$ in the infinite-small interval $[t_i , t_i + dt)$:
- $\lambda_{k}^{*}\left(t_{i}\right) d t$ 

the probability of not observing any events of type $k$ in the rest of interval $[0, T]$:
- $\exp \left(-\int_{0}^{T} \lambda_{k}^{*}(u) d u\right)$


---

Computing the integral $\int_0^T$

For continous-time model, could use Monte Carlo integration [Mei and Eis- ner, 2017] or numerical quadrature [Rubanova et al., 2019; Zuo et al., 2020].

For autoregresson TPP,  we could compute the NLL analytically.


# Training

TPP parameters are learned by Mini NLL, using some variant of (stochastic) gradient descent.



