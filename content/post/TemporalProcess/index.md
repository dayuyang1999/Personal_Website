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


Reference 

- [MathWorld Hawkes Process](https://mathworld.wolfram.com/HawkesProcess.html)
- [The Original Paper by Alan Hawkes](https://www-jstor-org.ezproxy.lib.utexas.edu/stable/2334319?seq=1#metadata_info_tab_contents)
- [A paper has relatively clear derivation](https://onlinelibrary.wiley.com/doi/full/10.1002/fut.21644)

---




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








End