---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Pytorch Basics 2: Model"
subtitle: ""
summary: ""
authors: []
tags: 
- Pytorch
categories: []
date: 2021-10-22T10:51:39-04:00
lastmod: 2021-10-22T10:51:39-04:00
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

| First first, the `forward` of nn.Module is the one single most important part of the entire implementation. It may effect:
  - other part of the `nn.Module`
  -  customized dataloader
  -   optimizer 
  -   and loss function.

# Component

Your DIY model must inherit from [`nn.Module`](https://pytorch.org/docs/stable/generated/torch.nn.Module.html).

And must contains 2 functions:
- `__init__`
  - define all trainable layers and other parameters
  - things begin with `self` and `nn` will be included to `model.parameters()` by default
```python
#e.g.:

self.d1 = nn.Linear()
self.x = nn.Parameter
self.y = 2 # this will not included
self.z = [nn.Linear, nn.Linear] # this will also not inlcuded

```

- `forward`
  - input is a batch of data
  - describe how data pass through the network 
  - and generate some output (logits)


# GPU

move model to GPU if avaliable
- forward operations are made in GPU
- model and a batch of data will be loaded to GPU

```python

#put tensor to GPU
ta = torch.tensor(...).to('cuda:0')

# if I need to call some numpy method
# e.g. using sklearn metrics
l = (...).cpu().numpy()
l2 = np.function(l)

```

# Commonly used `nn.`

### `nn.ModuleList` vs `nn.Sequential`
+ `nn.ModuleList` is a module list, literally. A list store modules. See that as a Python list
+ `nn.Sequential` must run sequentially

### Dropout
when training, must specify `training`: e.g.
```python
x = F.dropout(x, p=self.dropout, training=self.training) # test time, pass everything; training time, dropout
```

# Examples

```python

# Get cpu or gpu device for training.
device = "cuda" if torch.cuda.is_available() else "cpu"
print("Using {} device".format(device))

# Define model
class NeuralNetwork(nn.Module):
    def __init__(self):
        super(NeuralNetwork, self).__init__()
        self.flatten = nn.Flatten()
        self.linear_relu_stack = nn.Sequential(
            nn.Linear(28*28, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, 10)
        )

    def forward(self, x):
        x = self.flatten(x)
        logits = self.linear_relu_stack(x)
        return logits

model = NeuralNetwork().to(device)
print(model)





```