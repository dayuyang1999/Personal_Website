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

Suppose we have a counting process whose `intensity function` at time t depends on 2 part of random inputs. (Note: For Possion Process, intensity function is a contant at time t;)
- now (1 term)
- history (not just 1 term)

For each event, there is a base intensity function $\lambda_{0}(t)>0$, called `mark`.(`mark` is independent of any previous event).

So, $Y_{i}$ being the mark of event $i$, for $i=1, \ldots, N(t)$, we have CIF as:

$$\lambda\left(t \mid \mathcal{H}\\_{t}\right)=\lambda\\_{0}(t)+\sum\\_{i=1}^{N(t)} Y\\_{i} e^{-\delta\left(t-T\\_{i}\right)}$$
- previous events' mark must decrese over time at an exponential rate $\sigma$

Following the definition of counting process:

$$\mathbb{P}\left(N(t+h)-N(t)=1 \mid \mathcal{H}\\_{t}\right)=\lambda\left(t \mid \mathcal{H}\\_{t}\right) h+o(h)$$
$$\mathbb{P}\left(N(t+h)-N(t) \geq 2 \mid \mathcal{H}\\_{t}\right)=o(h)$$


This kind of counting process (having a random intensity function) is `Hawkes Process`.



