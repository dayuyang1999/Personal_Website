---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Marked VS Multi-dim TPP"
subtitle: "How to differentiate them"
summary: ""
authors: 
- Dylan Yang
tags: 
- Point Process
categories: []
date: 2021-09-25T12:07:02-04:00
lastmod: 2021-09-25T12:07:02-04:00
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

They are targeting "multi" on `type of interaction`, `Node` respectively:
- Marked: Multi- types of interaction 
- MTPP: Multi- number of Node


**One easy way to differntiate them is by the likelihood function:**

(Mei and Eisner, 2020.) is modeling marked TPP:

$$
\ell=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\underbrace{\int_{t=0}^{T} \lambda(t) d t}_{\text {call this } \Lambda}
$$
- the left (positive sample) has "multi" ($k$ in this case)
- the right (negative sample) has no multi.

and there's a `mark generation function`:

$$
\hat{k}_{i}=\operatorname{argmax}_{k} \int_{t_{i-1}}^{\infty} \frac{\lambda_{k}(t)}{\lambda(t)} p_{i}(t) d t
$$


---


