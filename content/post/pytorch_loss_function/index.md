---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Pytorch Basics 3: Loss Function"
subtitle: ""
summary: ""
authors: []
tags: []
categories: []
date: 2021-10-22T16:52:48-04:00
lastmod: 2021-10-22T16:52:48-04:00
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

To train a model, we need:
- loss function
- Optimizer
- model
- data


# Relation beetween Loss function and Optimizer?

recall the simplest pytorch training process:
```python

dataset = My_data()
loader = DataLoader(dataset)
model = MyNet()
criterion = torch.nn.CrossEntropyLoss()
optimizer = SGD(model.parameters)
  for epoch in range(10):
    for batch, labels in loader:
    outputs = model(batch)
    loss = criterion(outputs, labels)
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()



```

here criterion is the loss function.

`Loss` is not directly related to the `Optimizer`, `loss` is directly related to `Gradients`.
- Loss computes the gradient at `loss.backward()`
- the optimizer looks at the gradient over all parameters in `model.parameters()` and then update them using `optimizer.step()`


# Loss Function

Generally, loss funciton could be any function using Pytorch Operation that.

There are two categories of loss:
1. Error Loss
   1. use the output of model as the prediction, and compare with the true label
2. Likelihood Loss
   1. the goal of your optimizer is to make the overall likelihood of `observations` as large as possible
      1. there is no "target" and "logits"
      2. there is only "positive samples" and "negative samples"


Error Loss is more focusing on "predictive power", and likelihood loss is trying to fit the observation data.

```python

# a general error loss

def error_loss(output, target):
  '''
  output is the output of a single forward pass
  '''
  ...
  ...
  return a_slacar # bigger = worse performance

# a general likelihood loss

def likelihood_Loss(positive, negative):
  '''
  output is the negative log likelihood
  '''
  L =  torch.cumsum(positive) - torch.cumsum(negative)
  return -L # bigger = worse fitting


```