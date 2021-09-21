---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Point Process 3.1: Parameterize $\lambda$, Training, Prediction and Evaluation: The Neural Hawkes Process"
subtitle: ""
summary: ""
authors: 
- Dylan Yang
tags: 
- Statistics, Recurrent Neural Network
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

By observing $\left[\left(k_{1}, t_{1}\right),\left(k_{2}, t_{2}\right), \ldots,\left(k_{T}, t_{T}\right)\right]$ sequence as training data, how to train the point process model? How could we predict? How to tell if the prediction is reliable?

For parameterizing step:


For training step, should be clear:
- what is the parameter to learn
- what is the likelihood function 
- the algorithm enables learning

For prediction:
- what is the algorithm

For evaluation:
- what is the metrics.




# Problem Description
Modeling K types of events, each of which are observed to occur in continuous time.

observed event streams $\left(k_{1}, t_{1}\right),\left(k_{2}, t_{2}\right), \ldots$
- $k_{i} \in\{1,2, \ldots, K\}$ is an event type
- $0<t_{1}<t_{2}<\cdots$ are times of occurrence





# Parameterizing $\lambda$: Continus LSTM hidden state

[The LSTM we are familar with]() is the discrete version. However, $\lambda(t)$ is time dependency. 

to make it continuous, the author make 












For a general point process, likelihood function:

$$\ell=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\underbrace{\int_{t=0}^{T} \lambda(t) d t}_{\text {call this } \Lambda}$$

