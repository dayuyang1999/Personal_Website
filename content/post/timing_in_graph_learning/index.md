---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Timing in graph learning"
subtitle: ""
summary: "modeling continuous timing in temporal graph learning"
authors: 
- Dylan Yang
tags: 
- Graph Neural Network
categories: []
date: 2021-09-22T15:41:33-04:00
lastmod: 2021-09-22T15:41:33-04:00
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


# How to model "Timing" in Temporal Graph Learning: 

For predictive task in temporal graph dynamics, there're two kinds of models classfied by how they deal with time: 
- continuous predictive
  - Dyrep, lDG, ...
- discrete predictive
  - NRI, dNRI, ...

Some tasks, for example, predicting the trajectory of a ball, predicting what is the next movement of a basketball player,... people don't care much about the "timing", or "when the next movement/interaction/dynamics is gonna happen". So they ignore the timing information like traditional LSTM.


Generally, "continuous predictive" is attactive for those tasks that timing is important, which means you want to know "what is the next dynamic" and "when the next dynamic will happen" at the same time. Such as stock price fluctuation prediction, market deal prediction...

<br><br>

Previous works have develop lots of method to modeling continuous time into existing Graph Learning architecture.


**Point Process Party**

(Mei and Eisner 2017) is who invent "Neural Hawkes Process" to modeling continuous events sequence under various scenarios(not a graph method). (Han et al. 2020) borrow the idea of (Mei and Eisner 2017) and propose the "Graph Neural Hawkes Process" to modeling dynamics of evolving graph.

(Trivedi et al. 2019) drop the original formulation of intensity function $\lambda$ in Hawkes Process and parameterized the $\lambda$ by a temporal-attentive representation network. Present "DyRep" to modeling continuous complex Temporal Graph Interactions.


**Time2Vec Party**

The main difficulty of dealing with contious time is that "timing" feature seems to be not compatible with majority of Graph Learning Architectures, where everything is represented as a vector.

(Kazemi et al. 2019) is the first work which providing a **model-agnostic(模型无关)**vector
representation for time, called "Time2vec". [Here](https://github.com/dayuyang1999/Date2Vec) he provided several pretrained models to convinently convert datetime to vector.

After this, researcher could easily consider timing features in their existing architecture.(Just simply concatenate one more timing vector to input). (Rossi et al. 2020) use time2vec in his general temporal dynamic graph learning architecture. 