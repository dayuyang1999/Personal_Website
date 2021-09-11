---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Python Module Import Error"
subtitle: ""
summary: "You successfully installed the module but still unable to import it"
authors: ["Dayu Yang"]
tags: []
categories: []
date: 2021-09-10T20:56:55-04:00
lastmod: 2021-09-10T20:56:55-04:00
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


Sometimes, even if we successfully installed the python package in terminal (typically use `pip install Module Name`), we still unable to load the package in Python Code. 

Please read [python offical module document](https://docs.python.org/zh-cn/3/tutorial/modules.html#the-module-search-path) before reading the following content.

# Possible Solutions

## 1. Same Conda Env
If you use conda (a env control tool)
please make sure you are installing the module to the same env that you are using as interpreter.


## 2. Add `PYTHONPATH`

If you confirmed that the module is installed in the env that you are currently use as interpreter. You should check if the `dir to the package` is writen under `PYTHONPATH`:

### 1st, found where the module is (in the terminal):

```bash
conda list ModuleName

```

This is an example that what terminal told me (wrds is a module name):

```bash

# packages in environment at /home/username/miniconda3/envs/myenvname:
#
# Name                    Version                   Build  Channel
wrds                      3.1.0                    pypi_0    pypi
(myenvname) username@my_mac:~/miniconda3/envs/myenvname$

```

So the package is located in `/home/username/miniconda3/envs/myenvname`

### 2nd, add this dir to the `PYTHONPATH`

use the kernel `myenvname` as Python interpreter, and run:

```python

 import sys
 for p in sys.path:
     print(p)

```

If `/home/username/miniconda3/envs/myenvname` is not in the `PYTHONPATH` list, congrads! add it mostly solve the problem. Run these python code:

```python

import os
path = "/home/username/miniconda3/envs/myenvname"
sys.path.append(path)

```

Then, reload the package:

```python

import wrds


```
