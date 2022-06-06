---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Graph Gym Learning 0: The intuition"
subtitle: "The intuition"
summary: ""
authors: 
- Dylan Yang
tags:
- Graph Neural Network
categories: []
date: 2021-09-17T12:28:27-04:00
lastmod: 2021-09-17T12:28:27-04:00
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

This Note was my personal review of [GraphGym](https://snap.stanford.edu/gnn-design/) Intro in [Stanford Computer Forum - Graph Learning Workshop: Sept 16, 2021](https://www.youtube.com/watch?v=NKZdqCi5fVE) presented by [Jiaxuan You](https://cs.stanford.edu/people/jiaxuan/).

**Some new feature may be added after this post**

---

I repeat the important resources mentioned in this note here:

1. [GraphGym](https://snap.stanford.edu/gnn-design/), a design space for GNN
2. Emmerging stars in GNN research, [Jiaxuan You](https://cs.stanford.edu/people/jiaxuan/) and [Rex Ying](https://cs.stanford.edu/people/rexy/index.html)
3. [Jure Leskovec related GNN resources](http://snap.stanford.edu/index.html)
4. [Jure Leskovec's News](https://cs.stanford.edu/people/jure/)


---

- [Graph Learning Basics](#graph-learning-basics)
  - [Machine Learning Tasks on Graph](#machine-learning-tasks-on-graph)
  - [General Pipeline of Deep Learning in Graph](#general-pipeline-of-deep-learning-in-graph)
  - [Graph Neural Net](#graph-neural-net)
- [Challenges in Graph Learning](#challenges-in-graph-learning)
  - [Implementation](#implementation)
  - [Finding the best GNN design](#finding-the-best-gnn-design)
  - [Customizing a GNN pipeline](#customizing-a-gnn-pipeline)
- [Intro of GraphGym](#intro-of-graphgym)
  - [GNN Design Space of GraphGym](#gnn-design-space-of-graphgym)
  - [Experiment Configuration](#experiment-configuration)
  - [Launching a Batch of Experiments (for Model/Hyperparam/Data... Comparision)](#launching-a-batch-of-experiments-for-modelhyperparamdata-comparision)
    - [Auto Summary](#auto-summary)
  - [Register Customized Module](#register-customized-module)
- [GraphGym in Research](#graphgym-in-research)
- [Future and History](#future-and-history)


---

# Graph Learning Basics 

## Machine Learning Tasks on Graph

- graph-level prediction
- node-level prediction
- edge-level prediction 

## General Pipeline of Deep Learning in Graph

![](https://cdn.mathpix.com/snip/images/CjEVAn0kPHjBdklY08T7-er50cuYIevvGEAeMnJCWtY.original.fullsize.png)

**the key is to learn the node embedding from data**

## Graph Neural Net

Key idea: iteratively aggregate info from neighbors (Message Passing Mechanism)

1. Define computation graph by graph structure
2. propagate info


# Challenges in Graph Learning

## Implementation

Although [PyG](https://pytorch-geometric.readthedocs.io/en/latest/) offer some built-in module ready to apply, there is still a huge gap:

![](https://cdn.mathpix.com/snip/images/YUTcpVkR2oM7OD1S0MuK4r2vZTgy6HfhxdEqtF6EJKs.original.fullsize.png)

- How to manipulate data
- How to design GNN
- How to predict
- How to evaluate (performance)


## Finding the best GNN design

Key: GNN task is unlike NLP or CV. Model performance vary a lot according to the specific predictive task and data.

![](https://cdn.mathpix.com/snip/images/4ZbDutf47mba_qznx9ZzICUJibCv_cMRqMfArxh0xDM.original.fullsize.png)

Some possible Degree of freedom:
- GNN layer: GCN, GraphSAGE, GAT, ...
- Aggregation: Mean, Sum, Max,...
- Operators: BatchNorm? Dropout? L2norm?, ...



## Customizing a GNN pipeline

Let's say: you want to apply a newly invented GNN layer: CoolConv

The Ugly Old:
- Fork the open-source pipeline that also uses CoolConv, then Repurpose the pipeline to the dataset/task that you need
  - Issue: A new pipeline is needed, whenever you want to try something new
- Adapt CoolConv in your existing GNN pipeline: Replace GCNConv with CoolConv
  - Issue: Your core pipeline code needs to be altered, not scalable



The Builtiful New:

- easily `register` customized modules(See detail below)
- also enhance visibility


# Intro of GraphGym

Key: Modularized

A implementation example:
![](https://cdn.mathpix.com/snip/images/oyjLLhxg_UjomekZwjMGgXH-9syWBWVe88wHNfCJMzA.original.fullsize.png)

## GNN Design Space of GraphGym

- Main design dimensions
  - Intra-layer design
  - Inter-layer design
  - Learning configuration
- And others...

An example:
![](https://cdn.mathpix.com/snip/images/N4vkxznqJXvmxaFFD5fCAJMbdDDQXWykoyzTh6_1gwI.original.fullsize.png)


## Experiment Configuration

Each Design Space is fully described by the ` configuration file`.
- You can always reproduce the experiment, by ONLY keeping this config file, for example:

```bash
python main.py --cfg example_node. yaml --repeat 3
```


An example of ` configuration file`:
![](https://cdn.mathpix.com/snip/images/-QO2HUlZLHFFxLKHx8_8PrlImkESBYnhSRR8kxidj3o.original.fullsize.png)


## Launching a Batch of Experiments (for Model/Hyperparam/Data... Comparision)

Grid Search:
![](https://cdn.mathpix.com/snip/images/91pFoYrcT_jCoE3NzPIiMOhA_Qhs7DmuiLTKf9h2OgQ.original.fullsize.png)

Launching experiments in Parallel:
![](https://cdn.mathpix.com/snip/images/XLEs91ZzzCNvwh5StyLiFVHbjTzgQlLlYT7Zg13hgV8.original.fullsize.png)


### Auto Summary
The GraphGym can auto summarize experiment results and figures

Result:

Each line is a experiment:
![](https://cdn.mathpix.com/snip/images/_un85XteJ0blv7LQY1aAwSRZYBKA9NGfKgDViuk-sYQ.original.fullsize.png)

Each Column of plos is a degree of freedom:

![](https://cdn.mathpix.com/snip/images/_mR8dIg7fJjZbfin41zCd6pYRkwWnd3WFodRLb2mbQo.original.fullsize.png)

For example, see aggregation column, there're 3 aggregation method: `max`,`mean`, and `sum`. result shows `sum` always rank the 1st. So `sum` is the best choice for `aggregation layer`.


## Register Customized Module

For example, I desgin a new module named `ExampleConv`

![](https://cdn.mathpix.com/snip/images/poDisyqBM-2C_vd21S9zz2eQIropjwqxjYG-QVM_m4E.original.fullsize.png)

Then, register this GNN layer to GraphGym by:
```bash
register_layer('exampleconv', ExampleConv)

```

Then, the new `ExampleConv` layer is ready to be used:

![](https://cdn.mathpix.com/snip/images/KK2rtixfM1Qcl2picbzZbKEnXOmGde-ya7KauooKC_E.original.fullsize.png)

Here is the full list of supported customized modules:
- Activations
- Customized configurations
- Feature augmentations
- Feature encoders
- GNN heads
- GNN layers
- Data loaders
- Loss functions
- GNN network architectures
- Optimizers
- GNN global pooling layers
- GNN stages
- GNN training pipelines
- Data transformations


# GraphGym in Research 

Goal: Use GraphGym to validate a newly proposed GNN layer (`ID-GNN`)


- GraphGym can ensure a fair comparison:
  - We fix all the other the hyperparameters except switching the GNN Layer
  - We control the computational budget for all the models to be the same
- We want to show ID-GNN can consistently outperform GCN
  - 4 GNN backbones 
  - 20 node/edge/graph level tasks 
  -  3 random seeds 
  -  2 GNN layers (ID-GNN vs. GCN) 
    - =480 experiments

The GraphGym implementation:
![](https://cdn.mathpix.com/snip/images/oJP_s-TQXdM2qWGH3zf2pDeUSitTGLP_ABWFL1fU5eE.original.fullsize.png)


# Future and History

- GraphGym has become a core component of PyG $2.0$

```bash
pip install torch_geometric 
git clone https://github.com/pyg-team/pytorch_geometric.git 
bash graphgym/run_single.sh # run a single GNN experiment 
bash graphgym/run_batch.sh # run a batch of GNN experiments
```


