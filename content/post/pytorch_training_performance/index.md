---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Pytorch Data Loader"
subtitle: ""
summary: ""
authors: 
- Dylan Yang
tags: 
- Pytorch
categories: []
date: 2021-10-09T14:00:27-04:00
lastmod: 2021-10-09T14:00:27-04:00
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

# Training Streams

Recall the Typical Training Process:

```python

from torch.optim import SGD
loader = ...
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

# Loader

Two styles of dataset:
- Streams 
  - providing 1 sample each iter
- Map
  - allow access samples in any order
    - e.g. randomly
  - user may augment, manipulate data through dataloader


```python
class IterableStyleDataset(torch.utils. data. IterableDataset):
  def _iter_(self):
  # Support for streams...


class MapStyleDataset(torch.utils. data.Dataset):
  def _getitem_(self, key):
  # Map from (non-int) keys $\ldots$
  def _len_(self):
  # Support sampling ...



```

## `DataLoader` Object 

Pytorch DataLoader allows us to load batches from a dataset

```python
dataloader = DataLoader (
  dataset, # only for map-style dataset
  batch_size=8, # balance speed and convergence
  num_workers =2, # non-blocking when $>0$
  sampler=RandomSampler, # random read may saturate drive
  pin_memory=True, # page-lock memory for data?


```
+ random read may saturate the storage
+ pin: keep the data in RAM.
  + you may transfer data to GPU faster


# Performance

2 main constrains:
- CPU IPS (instructions per second)
- storage IOPS (I/O per second)

## CPU
You want the CPUs to be performing:
- preprocessing
-  decompression
-  copying – to get the data to the GPU. 

The rule: You don’t want them to be idling or busy-waiting for thread/process synchronization primitives, IO, etc

The easiest way to improve CPU utilization with the PyTorch is to use the `worker` process support built into Dataloader.

