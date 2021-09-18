---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "GNN Environment Configuration"
subtitle: "Should be careful when doing Configuration. Different versions of Compiliers do not play nice with each other..."
summary: ""
authors: 
- Dylan Yang
tags: 
- Graph Neural Network
categories: []
date: 2021-09-17T14:20:47-04:00
lastmod: 2021-09-17T14:20:47-04:00
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

Generally, NEVER use `pip install something` !!! This may lead to fatal result on your environment.

You have to specify which verison of `something` you intend to install.

# From 0 to 100

Here is a stable version of Graph Research Environment provided by [Xiaohang Zhao](https://www.linkedin.com/in/xiaohangzhao/).

```bash
conda create --name GNN python=3.8.10
conda activate GNN
conda install tqdm ipykernel=5.3.4 numpy=1.20.2 numba pandas=1.2.4 scikit-learn=0.24.2 scikit-learn-intelex
# there will be a message saying that to run scripts with accelerated scikit-learn, using
# python -m sklearnex my_application.py
python -m ipykernel install --user --name=GNN
conda install -c conda-forge ipywidgets
jupyter nbextension enable --py widgetsnbextension
# jupyter contrib nbextension install --user

# install pytorch
conda install pytorch=1.8.0 torchvision torchaudio cudatoolkit=10.2 -c pytorch


# install PyTorch geometric
pip install torch-scatter==2.0.7 -f https://pytorch-geometric.com/whl/torch-1.8.0+cu102.html
pip install torch-sparse==0.6.9 -f https://pytorch-geometric.com/whl/torch-1.8.0+cu102.html
pip install torch-cluster==1.5.9 -f https://pytorch-geometric.com/whl/torch-1.8.0+cu102.html
pip install torch-spline-conv==1.2.1 -f https://pytorch-geometric.com/whl/torch-1.8.0+cu102.html
pip install torch-geometric==1.7.0

# install DGL
conda install -c dglteam dgl-cuda10.2
python -m dgl.backend.set_default_backend pytorch

# install other useful packages
pip install torch-geometric-temporal==0.37
pip install deepsnap==0.2.0
pip install captum==0.3.1


# remove the environment
conda deactivate
conda remove --name GNN --all 
jupyter kernelspec uninstall gnn
# remove all the caches created by conda
conda clean --all
```


# from 50 to 100

Basically, you have to pre-check the version of (if you already directly use `pip install xxx` to install the following module):
- Pytorch
- Nvidia CUDA Compiler (NVCC)
- Nvidia GPU Driver 
- Pytorch Geometric

check torch version:

```bash
conda activate GNN
python3
import torch
print(torch.__version__)
exit()
```


check CUDA Compiler Version:
```bash
nvcc
```

check Nvidia GPU Driver Version:
```bash
nvidia-smi
```

For more detail, check out [Pytorch Docs](https://pytorch.org/get-started/locally/)

# Common Issues

