---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Bipartite Dynamic Representations: Bidyn"
subtitle: "Bipartite Dynamic Representations (BiDyn) is a general approach to transductive node classification on dynamic bipartite graphs, with additional components for the task of detecting abusive behavior in online communities."
summary: ""
authors: 
- Dylan Yang
tags: 
- Graph Neural Network
categories: []
date: 2021-09-21T18:18:30-04:00
lastmod: 2021-09-21T18:18:30-04:00
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

Clarification: This Note is based on the presentation of Rex Ying in [Youtube](https://www.youtube.com/watch?v=NKZdqCi5fVE). Some related resources:
- [Slides](https://snap.stanford.edu/graphlearning-workshop/slides/stanford_graph_learning_Abuse_detection.pdf)
- [Code](https://github.com/qema/bidyn)
- [Short Intro in SNAP](http://snap.stanford.edu/bidyn/)


---

A node classification problem that suitable for

- bipartile 
- Dynamic
- large-scale

graph


---

The model is specialized to detect abuse user in Amazon.
![](https://cdn.mathpix.com/snip/images/qyZCZjrk-zzyr7BYWyANyHeQaTVSw-eidAuQRO-lSyw.original.fullsize.png)


# Problem Define

## Data Description:

Model data as a dynamic, bipartite graph $G=(U, V, E, f, g, h)$
- User nodes $u \in U$, item nodes $v \in V$
- Dynamic edges $(u, v, t) \in E$ ( $u \in U, v \in V, t \in \mathbb{R}$ ) with timestamp $t$
- Node features $f: U \rightarrow \mathbb{R}^{d_{1}}, g: V \rightarrow \mathbb{R}^{d_{2}}$
- Edge features $h: E \rightarrow \mathbb{R}^{d_{3}}$


## Problem

Model learning problem as transductive node classification
- Some users are known to be abusive or non-abusive
- Partial function $f: U \rightarrow\{0,1\}$ known at training time
- Goal: infer status of remaining users
- Predict labels of all users $u \in U$


## Chanllenge

- No ground-truth labels
- complex dynamic
- extreme large scale






