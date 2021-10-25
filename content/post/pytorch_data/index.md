---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Pytorch Basics 1: Data "
subtitle: ""
summary: ""
authors: []
tags: 
- Pytorch
categories: []
date: 2021-10-22T10:18:42-04:00
lastmod: 2021-10-22T10:18:42-04:00
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




PyTorch has two primitives to work with data: `torch.utils.data.DataLoader` and `torch.utils.data.Dataset`.
-   `Dataset` is an object that stores the samples and their corresponding labels
    -   there are two types of `Dataset`: https://pytorch.org/docs/stable/data.html
        -   for map-style dataset, must contains two functions:
            -   `__getitem__`
            -   `__len__`
- DataLoader makes your dataset to an iterable. 
  - then you could use for loop to generate batch of data





# Examples


```python
######### e.g. 1


# Download training data from open datasets.
training_data = datasets.FashionMNIST(
    root="data",
    train=True,
    download=True,
    transform=ToTensor(),
)

# Download test data from open datasets.
test_data = datasets.FashionMNIST(
    root="data",
    train=False,
    download=True,
    transform=ToTensor(),
)


batch_size = 64

# Create data loaders.
train_dataloader = DataLoader(training_data, batch_size=batch_size)
test_dataloader = DataLoader(test_data, batch_size=batch_size)

for X, y in test_dataloader:
    print("Shape of X [N, C, H, W]: ", X.shape)
    print("Shape of y: ", y.shape, y.dtype)
    break

# Shape of X [N, C, H, W]:  torch.Size([64, 1, 28, 28])
# Shape of y:  torch.Size([64]) torch.int64



######### e,g, 2



```