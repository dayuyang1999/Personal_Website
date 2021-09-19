---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Point Process: Hawkes Process 1: Introduction"
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


The key characteristic:
- **"self-excite"**:  each arrival increases the rate of future arrivals for some period of time
  - Each arrival excites the process in the sense that the chance of a subsequent arrival is increased for some time period after the initial arrival(So, lots of events fit this kind of process, e.g. the collapse of a bank could send shock-waves to other banks)

<br/><br/>
<br/><br/>

# Definiton of Conditional Intensity Function
Note: $\lambda^*$ ($\lambda$ here should be $\lambda^*$)  means "conditional intensity function" since it is conditional on the
past right up to but not including the present

$$\lambda* = \lambda\left(t \mid \mathcal{H}\_{t}\right)$$




---
**Definition 1**

(Conditional intensity function) Consider a counting process $N(\cdot)$ with associated histories $\mathcal{H}(\cdot) .$ If a (non-negative) function $\lambda^{*}(t)$ exists such that
$$
\lambda^{*}(t)=\lim \_{dt \downarrow 0} \frac{\mathbb{E}[N(t+dt)-N(dt) \mid \mathcal{H}(t)]}{dt}
$$

- this is saying: CIF is the number of event happnening in a very short period of time $dt$.


On the other hand, you may interpret as:


$$\lambda^*(t) = \mathbb{E'}[N(t+ dt) - N(t)\mid \mathcal{H}(t)]]$$

So:

$$\lambda^*(t) dt = \mathbb{E}[N(t+ dt) - N(t)\mid \mathcal{H}(t)]]$$

---
If you know point process, you know that "*a given amount of* event happens in a given bounded period of time" is a RV, it should have its own pdf $f$ and cdf $F$. What is the relationship between $\lambda$ and both of them?

---

**Note:**

Here we replace "a given amount of" as "a", since we only consider the Hawkes process, which is a counting process satisfies:

$$
\mathbb{P}(N(t+h)-N(t)=m \mid \mathcal{H}(t))=\left\{\begin{array}{ll}
\lambda^{*}(t) h+\mathrm{o}(h), & m=1 \\
\mathrm{o}(h), & m>1 \\
1-\lambda^{*}(t) h+\mathrm{o}(h), & m=0
\end{array}\right.
$$

---





The relation between conditional intensity function and the density function is:

$$
\lambda^{*}(t)=\frac{f^{*}(t)}{1-F^{*}(t)}
$$

This formulation can be interpreted heuristically in the
following way:





<br><br>
<br><br>

# Definition of Hawkes Process

Suppose we have a counting process whose `intensity function` at time t depends on 2 part of random inputs. (Note: For Possion Process, intensity function is a contant at time t)
- now (1 term)
- history (not just 1 term)

For each event, there is a base intensity function $\lambda\_{0}(t) \ne 0$ (avoid trivial cases as Possion Process).

So, $Y_{i}$ being the influence of historical event $i$, for $i=1, \ldots, N(t)$, we have CIF as: 

$$\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lambda\_{0}(t)+\sum\_{i=0}^{N(t)} Y\_{i} e^{-\delta\left(t-T\_{i}\right)}$$

- previous events' background intensity must decrese over time at an exponential rate $\delta$

This kind of counting process (having a random intensity function) is `Hawkes Process`.

Property:
1. $\lambda\left(0 \mid \mathcal{H}\_{0}\right)=\lambda\_{0}(0)$
2. whenever an event occurs, the intensity increases by the value of the event's background intensity;
3. if there are no events between time $s$ and time $s+t$ then:
    $$\lambda\left(s+t \mid \mathcal{H}\_{s+t}\right)=\lambda\_{0}(s+t)+\left(\lambda\left(s \mid \mathcal{H}\_{s}\right)-\lambda\_{0}(s)\right) e^{-\delta t}$$

---
Derivitive of 3:
$$
\begin{array}{l}
\lambda\left(s+t \mid H\_{s+k}\right)=\lambda\_{0}(s+t)+\sum\_{i=1}^{N(t+s)} Y\_{i} e^{-\delta\left(t-T\_{i}\right)} \\\\
\lambda\left(s \mid H\_{s}\right)=\lambda\_{0}(s)+\sum\_{i=1}^{N(s)} Y\_{i e}^{-\delta\left(t-T\_{i}\right)}
\end{array}
$$

$$N(t+s)=N(s) \;\;\text{Since no event happen in (t+s,s)}$$

---

According to Property 2, the intensity increases each time an event occurs, the Hawkes process is said to be a *self-exciting* process.

<br/><br/>
<br/><br/>


# General Definition of Self-Exciting Process

recall the general definition of `intensity function`:

$$
\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lim \_{h \downarrow 0} \mathbb{E}\left[\frac{N(t+h)-N(t)}{h} \mid \mathcal{H}\_{t}\right]
$$

or:

$$
\mathbb{P}(N(t+h)-N(t)=m \mid \mathcal{H}(t))=\left\{\begin{array}{ll}
\lambda^{*}(t) h+\mathrm{o}(h), & m=1 \\
\mathrm{o}(h), & m>1 \\
1-\lambda^{*}(t) h+\mathrm{o}(h), & m=0
\end{array}\right.
$$


- This is saying: the `intensity function` should indicate : "the number of event happened in a infinite-short period of time."

For a `Self-Exciting Process`(Hawkes Process is a special `Self-Exciting Process`), the `intensity function` is defined as:

$$
\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lambda\_{0}(t)+\int\_{0}^{t} \nu(t-s) \mathrm{d} \mathrm{N}(\mathrm{s})
$$
- $\lambda_0$ is a deterministic-based function; named `background intensity`.
- $v$ is a `kernel`, which represent the **positive** influence of the past events on the current value of the intensity process. named `excitation function`.

<br/><br/>
<br/><br/>

## Derive Hawkes Process from Self-Exciting Process

A Hawkes process is a self-exciting process with **exponential kernel**.

A Hawkes process is a self–exciting process with exponential kernel: $\nu(t)= Y\_{j} e^{-\delta_{j} t}$

So that the intensity becomes:

$$
\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lambda\_{0}+\int\_{0}^{t} Y e^{-\delta(t-s)} \mathrm{d} \mathrm{N}(\mathrm{s})
$$

![](https://cdn.mathpix.com/snip/images/qlRIykYwX0V0sEsgEaZ_TAC_ktqGj4WKjHc0T4NvpOM.original.fullsize.png)


<br/><br/>
<br/><br/>








End