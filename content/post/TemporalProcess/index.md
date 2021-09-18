---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Point Process: Hawkes Process"
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



# Definition of Hawkes Process

Suppose we have a counting process whose `intensity function` at time t depends on 2 part of random inputs. (Note: For Possion Process, intensity function is a contant at time t)
- now (1 term)
- history (not just 1 term)

For each event, there is a base intensity function $\lambda_{0}(t)>0$, called `mark`.(`mark` is independent of any previous event).

So, $Y_{i}$ being the mark of event $i$, for $i=1, \ldots, N(t)$, we have CIF as: 

$$\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lambda\_{0}(t)+\sum\_{i=0}^{N(t)} Y\_{i} e^{-\delta\left(t-T\_{i}\right)}$$

- previous events' mark must decrese over time at an exponential rate $\delta$

This kind of counting process (having a random intensity function) is `Hawkes Process`.

Property:
1. $\lambda\left(0 \mid \mathcal{H}\_{0}\right)=\lambda\_{0}(0)$
2. whenever an event occurs, the intensity increases by the value of the event's mark;
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




# General Definition of Self-Exciting Process

recall the general definition of `intensity function`:

$$
\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lim \_{h \downarrow 0} \mathbb{E}\left[\frac{N(t+h)-N(t)}{h} \mid \mathcal{H}\_{t}\right]
$$
- This is saying: the `intensity function` should indicate : "the number of event happened in a infinite-short period of time."

For a `Self-Exciting Process`(Hawkes Process is a special `Self-Exciting Process`), the `intensity function` is defined as:

$$
\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lambda\_{0}(t)+\int\_{0}^{t} \nu(t-s) \mathrm{d} \mathrm{N}(\mathrm{s})
$$
- $\lambda_0$ is a deterministic-based function
- $v$ is a `kernel`, which represent the **positive** influence of the past events on the current value of the intensity process.

## Hawkes Process from Self-Exciting Process

A Hawkes process is a self-exciting process with **exponential kernel**.

A Hawkes process is a self–exciting process with exponential kernel: $\nu(t)= Y\_{j} e^{-\delta_{j} t}$

So that the intensity becomes:

$$
\lambda\left(t \mid \mathcal{H}\_{t}\right)=\lambda\_{0}+\int\_{0}^{t} Y e^{-\delta(t-s)} \mathrm{d} \mathrm{N}(\mathrm{s})
$$

![](https://cdn.mathpix.com/snip/images/a-6jhkkHE72Eja73igiNYg35tuBwxK3iyZf-gcDBJkQ.original.fullsize.png)

