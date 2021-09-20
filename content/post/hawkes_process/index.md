---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Point Process 2: Hawkes Process"
subtitle: ""
summary: "Hawkes Process is a special kind of point process with exponentail decay self-exiciting factor"
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


Reference 

- [MathWorld Hawkes Process](https://mathworld.wolfram.com/HawkesProcess.html)
- [The Original Paper by Alan Hawkes](https://www-jstor-org.ezproxy.lib.utexas.edu/stable/2334319?seq=1#metadata_info_tab_contents)
- [A paper has relatively clear derivation](https://onlinelibrary.wiley.com/doi/full/10.1002/fut.21644)

---


- [Self-exciting Process](#self-exciting-process)
- [Some note](#some-note)
- [The Likelihood Function of Hawkes Process](#the-likelihood-function-of-hawkes-process)
  - [General Likelihood Function for Point Process](#general-likelihood-function-for-point-process)
  - [Likelihood function for Hawkes Process](#likelihood-function-for-hawkes-process)
  - [Complexity Analysis](#complexity-analysis)


# Self-exciting Process


`Self-excited point process` is a special group of point process whose intensity depends on the history of the point process. 

More precisely, the point process is determined by the intensity $\lambda$ through the relations:

$$
\begin{array}{l}
\mathbb{P}\left[N_{t+h}-N_{t}=1 \mid \mathcal{F}_{t}\right]=\lambda_{t} h+o(h) \\
\mathbb{P}\left[N_{t+h}-N_{t}>1 \mid \mathcal{F}_{t}\right]=o(h) \\
\mathbb{P}\left[N_{t+h}-N_{t}=0 \mid \mathcal{F}_{t}\right]=1-\lambda_{t} h+o(h)
\end{array}
$$

- in a short period h, it's only possible to have max 1 event happens(or no event happens)
  - and the event is called "a jump of counting process $N(t)$"
- this definition did not give us the exact formulation of $\lambda$
- from this, we could write a definition of $\lambda$:

$$
\lambda(t)=\lim \_{h \rightarrow 0} \frac{\mathbb{P}\left[N_{t+h}-N_{t}=1 \mid \mathcal{F}_{t}\right]}{h}
$$


Using  ${t_1, t_2, . . . , t_k}$ to denote the observed sequence of past arrival times of the point process up to time t, the Hawkes conditional intensity is:

$$
\lambda^{*}(t)=\lambda+\sum_{t_{i}<t} \mu\left(t-t_{i}\right)
$$



Then, we need to parameterize $\lambda$.

The structure of $\lambda$ is quite flexible and only required:
- background intensity $\lambda >0$ 
- excitation function $\mu \ge 0$  and $\mu(x) =0 (x<0)$


Followed by (12) in (Hawkes 1971), in that case $\mu(t)=\alpha \mathrm{e}^{-\beta t}$, which is parameterised by constants $\alpha, \beta>0$:

$$\lambda^{*}(t)=\lambda+\int\_{-\infty}^{t} \alpha \mathrm{e}^{-\beta(t-s)} \mathrm{d} N(s)=\lambda+\sum\_{t\_{i}<t} \alpha \mathrm{e}^{-\beta\left(t-t\_{i}\right)}$$



---

# Some note

**Why Exponential Decay?**

The analytic solution of likelihood function could easily be obtained when $\mu$ decays exponentially.

**relation with the survival analysis case**

since hawkes process is only a special case of point process describe in [the previous post](https://imagoodboy.com/post/hawkes_prerequisite/), so everything described there still hold for hawkes process.



<br><br>
<br><br>

**An example of simulation of Hawkes Process**

![](https://cdn.mathpix.com/snip/images/qlRIykYwX0V0sEsgEaZ_TAC_ktqGj4WKjHc0T4NvpOM.original.fullsize.png)


<br/><br/>
<br/><br/>





# The Likelihood Function of Hawkes Process


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
<br><br>

**Some thought about theorem 1:**

If we use the logized verison, and make the summation of the 2nd term at first:

$$\begin{aligned}\ln L &=\sum_{i=1}^{k}\left[\ln (\lambda(t i))-\int_{t_{i-1}}^{t_{i}} \lambda(n) d u\right] \\\\ &=\left[\sum_{i=1}^{k} \ln \left(\lambda\left(\tau_{i}\right)\right)\right]-\left[\int_{0}^{t_{k}} \lambda(v) d u\right] \end{aligned}$$

the last term called `cumulative risk` $\Lambda$ is what we have already learned.

Intuitively, $-\Lambda$ term (which is $\le 0$ ) sums the log-proba






<br><br>
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

For $i \in\{2, \ldots, k\}$, let $A(i)=\sum_{j=1}^{i-1} \mathrm{e}^{-\beta\left(t_{i}-t_{j}\right)}$, so that:

$$A(i)=\mathrm{e}^{-\beta t_{i}+\beta t_{i-1}} \sum_{j=1}^{i-1} \mathrm{e}^{-\beta t_{i-1}+\beta t_{j}} \\\\ =\mathrm{e}^{-\beta\left(t_{i}-t_{i-1}\right)}\left(1+\sum_{j=1}^{i-2} \mathrm{e}^{-\beta\left(t_{i-1}-t_{j}\right)}\right) \\\\ =\mathrm{e}^{-\beta\left(t_{i}-t_{i-1}\right)}(1+A(i-1))$$

With the added base case of $A(1)=0, l$ can be rewritten as:

$$l=\sum_{i=1}^{k} \log (\lambda \mid \alpha A(i))-\lambda t_{k} \mid \frac{\alpha}{\beta} \sum_{i=1}^{k}\left[\mathrm{c}^{-\beta\left(t_{k}-t_{i}\right)}-1\right]$$

For the detail of the efficient Algorithm, refer to:
- Y. Ogata, Annals of the Institute of Statistical Mathematics 30(1), 243 (1978)
- S. Crowley. Point process models for multivariate high-frequency irregularly spaced data. http://vixra.org/pdf/1211.0094v6.pdf (2013). Working paper, retrieved on 10 Feb 2015





End