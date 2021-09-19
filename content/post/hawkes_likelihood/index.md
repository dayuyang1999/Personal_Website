---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Point Process: Hawkes Process 2: Likelihood"
subtitle: ""
summary: ""
authors: 
- Dylan Yang
tags: 
- Statistics
categories: []
date: 2021-09-17T20:51:00-04:00
lastmod: 2021-09-17T20:51:00-04:00
featured: false
draft: false
math: true

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

Recall that Hawkes Process is good in modeling contagion risk or clustering arrival of events in finance, insurance, economics and many other fields.

Here we first parameterize our Hawkes Process. In statistics, more parameter generally means more flexibility and a more powerful model. However, we also need more data to estimate those parameters.

Recall `Hawkes Process`: 

$$
\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lambda\_{0}+\int\_{0}^{t} Y e^{-\delta(t-s)} \mathrm{d} \mathrm{N}(\mathrm{s})
$$

the flexibility mainly came from 3 part:
- the : $\lambda\_{0}$
  -  parameter as $\lambda$
- the historical influence $Y$
  -  parameter as $\alpha$
- the decay factor $\delta$:
  -  parameter as $\beta$

<br><br>
<br><br>


# Likelihood Function 

to estimate $(\alpha, \beta, \lambda))$, Maximum Likelihood Estimation is a straghtforward approach when the likelihood function is tractable.

Here we provide the likelihood function of Hawkes process:

---
Theorem 1  Let $N(.)$ be a regular point process on $[0, T]$ for some finite positive $T$, and let $t_{1}, \ldots, t_{k}$ denote a realisation of $N(\cdot)$ over $[0, T] .$ Then, the likelihood $L$ of $N(\cdot)$ is expressible in the form
$$
L=\left[\prod\_{i=1}^{k} \lambda^{*}\left(t\_{i}\right)\right] \exp \left(-\int\_{0}^{T} \lambda^{*}(u) \mathrm{d} u\right)
$$

| see Proposition 7.2.III of Daley, Daryl J., and David Vere-Jones. An introduction to the theory of point processes: volume I: elementary theory and methods. Springer New York, 2003.

---
Proof of Theorem 1:



