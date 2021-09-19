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
commentable: true


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

Keep in mind, **only the "exponential decay" kind of excitation function gives us a tractable likelihood function.**

<br><br>
<br><br>

## General Likelihood Function for Point Process



---
**Theorem 1** 

Let $N(.)$ be a regular point process on $[0, T]$ for some finite positive $T$, and let $t_{1}, \ldots, t_{k}$ denote a realisation of $N(\cdot)$ over $[0, T] .$ Then, the likelihood $L$ of $N(\cdot)$ is expressible in the form
$$
L=\left[\prod\_{i=1}^{k} \lambda^{*}\left(t\_{i}\right)\right] \exp \left(-\int\_{0}^{T} \lambda^{*}(u) \mathrm{d} u\right)
$$

| see Proposition 7.2.III of Daley, Daryl J., and David Vere-Jones. An introduction to the theory of point processes: volume I: elementary theory and methods. Springer New York, 2003.

---

**Proof of Theorem 1:**

Therome 1 is actually not hard to prove.

From [my previous post](https://imagoodboy.com/post/hawkes_prerequisite/), we already derive the relation between the pdf $f$(or cdf $F$) and the intensity function $\lambda$:

$$
F^{*}(t)=1-\exp \left(-\int_{t_{k}}^{t} \lambda^{*}(u) \mathrm{d} u\right), \quad f^{*}(t)=\lambda^{*}(t) \exp \left(-\int_{t_{k}}^{t} \lambda^{*}(u) \mathrm{d} u\right)
$$


Recall that we assume there are k events realization happened in $t_{1}, \ldots, t_{k}$  of the counting process N() observed over $[0, T]$.

The joint likelihood is simply product $f$ together:

$$L=\prod_{i=1}^{k} f^{*}\left(t_{i}\right)=\prod_{i=1}^{k} \lambda^{*}\left(t_{i}\right) \exp \left(-\int_{t_{i-1}}^{t_{i}} \lambda^{*}(u) \mathrm{d} u\right)$$



---
<br><br>
<br><br>

## Likelihood function for Hawkes Process

For further analysis logize the likelihood function:

$$\begin{aligned} L &=\prod_{i=1}^{k}\left[\lambda\left(t_{i}\right) \exp \left(-\int_{t_{i-1}}^{t_{i}} \lambda(u) d u\right)\right] \\\\ \ln L &=\sum_{i=1}^{k}\left[\ln (\lambda(t i))-\int_{t_{i-1}}^{t_{i}} \lambda(n) d u\right] \\\\ &=\left[\sum_{i=1}^{k} \ln \left(\lambda\left(\tau_{i}\right)\right)\right]-\left[\int_{0}^{t_{k}} \lambda(v) d u\right] \end{aligned}$$

Notice that the last term of the log likelihood function $ln\;L$ is the `cumulative hazard` we introduced in [my previous post](https://imagoodboy.com/post/hawkes_prerequisite/), which normally be noted as $\Lambda(t)$.


<br><br>
<br><br>

---
**For now, since the first term is easy to analysis, we only focus on the second term of $ln\;L$: $\Lambda(t)$.**


Then, for Hawkes Process, we take advantage of the "Exponentail Decay" excitation function form to analytically obtain the likelihood function:


$$\Lambda(t)=\int_{0}^{t_{k}} \lambda(u) d u=\int_{0}^{t_{1}} \lambda(u) d u+\sum_{i=1}^{k-1}\left(\int_{t_{i}}^{t_{i+1}} \lambda(u) d u\right)$$
- Introduce the `Exponentail Decay`, the integral part of the last term: 

$$\left(\int_{t_{i}}^{t_{i+1}} \lambda(u) d u\right) = \int_{t i}^{t_{i + 1}}\left(\lambda+\sum_{t_{j}<u} \alpha e^{-\beta(u-t_j)}\right) d u$$

- Notice that the whole last contains 3 layers of summation

![](https://cdn.mathpix.com/snip/images/0VpyYA48akfWhD9s9kgZx0vomLwltPwv4q3jCAovyLw.original.fullsize.png)




<br><br>


$$\Lambda(t)=\int_{0}^{t_{1}} \lambda d u+\sum_{i=1}^{k-1}\left(\int_{t_{1}}^{t_{i+1}}\left(\lambda+\sum_{t_{j} < n} \alpha e^{-\beta(u-t j)}\right) d u\right)$$

We first analysis the Front Part of $\Lambda(t)$, since it's pretty straightforward:

- Front Part = everything before the 2nd $\lambda$

$$=\int_{0}^{t_{1}} \lambda \;d u+\sum_{i=1}^{k-1}\left(\int_{t i}^{t_{i+i}} \lambda \;d u\right)+\cdots$$
$$=\left(\lambda_{t_{k}}-\lambda_{t_{k-1}}\right)+\left(\lambda_{t_{k-1}}-\lambda_{t_{k - 2}}\right)+\cdots\left(\lambda_{t_{1}}-\lambda_{t_{0}}\right)+\cdots$$
$$=\lambda_{t_{k}}+\ldots$$


<br><br>

Ok, then the rest part:

$$=\cdots+\sum_{i=1}^{k-1}\left[\int_{t i}^{t_{i+1}}\left(\sum_{t_j < n} \alpha e^{-\beta\left(u-t_{j}\right)}\right) d u\right]$$

just move $\alpha$ to the outside
$$\cdots+\alpha \sum_{i=1}^{k-1}\left[\int_{t i}^{t_{i-1}}\left(\sum_{t_j<n} e^{-\beta(u-t j)}\right) d u\right]$$

Since the integral $\int_{t_i}^{t_{i+1}} \cdots du$ and the summation$\sum_{t_{j}<n}$ is targeting two seperated variables inside, $u$ and $t_j$, the order of integral and summation is interchangable:

$$\cdots+\alpha \sum_{i=1}^{k-1}\left[\sum_{t j<u}\left(\int_{t i}^{t_{i}+1} e^{-\beta(u-t j)} d u\right)\right]$$

Let's deal with the integral at first, simply use:

$$\int e^{-\beta x} d x=-\frac{1}{\beta}\left(e^{-\beta x}\right)$$

Notice that once finish the integral, the notation of outer summation will change, since $min(u) = t_i$:

$$\sum_{t_{j}<u} \Rightarrow \sum_{j=1}^{i}$$

And the result:

$$\cdots  -\frac{\alpha}{\beta} \sum_{i=1}^{k}\left[\sum_{j=1}^{i}\left(\left.e^{-\beta(u-t j)}\right|_{t_{i}} ^{t_{i+1}}\right)\right]$$

which is:

$$-\frac{\alpha}{\beta} \sum_{i=1}^{k-1}\left[\sum_{j=1}^{i}\left(e^{-\beta\left(t_{i+1}-t_{j}\right)}-e^{-\beta\left(t_{i}-t_{j}\right)}\right)\right]$$


Here we focus on the 2-layer summation:

$$\sum_{i=1}^{k} \sum_{i=1}^{i}(\cdots)$$

Intestingly, if you list them organizedly, you will found most of the terms cancel out with each other:

![](https://cdn.mathpix.com/snip/images/p22PsyUHB_-_4eBZdTE6IgZSsjtHCiZelGRo9_bsJ58.original.fullsize.png)

Here is the rest:

$$\sum_{i=1}^{k_{1}} \sum_{j=1}^{i}(\cdots)$$
$$= \sum_{i=1}^{k-1}\left(e^{-\beta\left(t_{k}-t_{1}\right)}-1\right)$$



Let's summarize everything together, the `cumulative hazard` $\Lambda(t)$ became:

$$\Lambda\left(t_{k}\right)=\lambda t_{k}-\frac{\alpha}{\beta} \sum_{i=1}^{k-1}\left[\mathrm{e}^{-\beta\left(t_{k}-t_{i}\right)}-\mathrm{e}^{-\beta\left(t_{i}-t_{i}\right)}\right]=\lambda t_{k}-\frac{\alpha}{\beta} \sum_{i=1}^{k}\left[\mathrm{e}^{-\beta\left(t_{k}-t_{i}\right)}-1\right]$$

---

<br><br>


Remember we only take care $\Lambda(t)$ instead of the whole likelihood function above? It's time to put $\Lambda(t)$ back:

$$ln L=\left[\sum_{i=1}^{k} \ln \left(\lambda\left(t_{i}\right)\right)\right]-\left[\int_{0}^{t_{k}} \lambda(u) d u\right] $$

$$= \left[\sum_{i=1}^{k} \ln \left(\lambda^*\left(t_{i}\right)\right)\right] -\lambda t_{k}+\frac{\alpha}{\beta} \sum_{i=1}^{k}\left[\mathrm{e}^{-\beta\left(t_{k}-t_{i}\right)}-1\right] $$


Recall:

$$\lambda^* = \lambda+\alpha \sum_{j=1}^{i-1} \mathrm{e}^{-\beta\left(t_{i}-t_{j}\right)}$$
- sometimes I miss the * at the top of $\lambda$.

We get the analytical version of likelihood function of Hawkes Process:

$$l=\sum_{i=1}^{k} \log \left[\lambda+\alpha \sum_{j=1}^{i-1} \mathrm{e}^{-\beta\left(t_{i}-t_{j}\right)}\right]-\lambda t_{k}+\frac{\alpha}{\beta} \sum_{i=1}^{k}\left[\mathrm{e}^{-\beta\left(t_{k}-t_{i}\right)}-1\right]$$


<br><br>

## Complexity Analysis

Although the likelihood function of Hawkes Process is tractable, the first term is computationally infeasible which entails $O(k^2)$ complexity.


Fortunately the similar structure of the inner sumation term $\sum_{j=1}^{\imath-1} \mathrm{e}^{-\beta\left(t_{i}-t_{j}\right)}$ allows the likelihood function to be computed with $O(k)$ complexity.

Refer to:
- Y. Ogata, Annals of the Institute of Statistical Mathematics 30(1), 243 (1978)
- S. Crowley. Point process models for multivariate high-frequency irregularly spaced data. http://vixra.org/pdf/1211.0094v6.pdf (2013). Working paper, retrieved on 10 Feb 2015


End