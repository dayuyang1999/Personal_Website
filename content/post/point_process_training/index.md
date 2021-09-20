---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Point Process 3: Training"
subtitle: ""
summary: ""
authors: 
- Dylan Yang
tags: 
- Statistics
categories: []
date: 2021-09-20T11:53:27-04:00
lastmod: 2021-09-20T11:53:27-04:00
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



If we observe a sequence of event, $\left(k_{1}, t_{1}\right),\left(k_{2}, t_{2}\right), \ldots,\left(k_{i-1}, t_{i-1}\right)$

For a general point process, likelihood function:

$$\ell=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\underbrace{\int_{t=0}^{T} \lambda(t) d t}_{\text {call this } \Lambda}$$

