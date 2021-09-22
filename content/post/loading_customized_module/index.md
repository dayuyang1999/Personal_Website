---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Loading Customized Module"
subtitle: ""
summary: "I made my own module DIYm and try to use it in another project"
authors: 
- Dylan Yang
tags: 
- Python Basics
categories: []
date: 2021-09-21T17:42:28-04:00
lastmod: 2021-09-21T17:42:28-04:00
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

Senario:

You download a module from Github which did not register in pip or conda. Or you DIY your own module.

How could you import them just like `numpy` or `pandas` using: `import numpy`, or `from pandas import read_csv` ?

---

2 things
- create a standarized `setup.py` file 
- register the module to `PYTHONPATH`/



---

# `setup.py` file 

```python

from setuptools import setup, find_packages

setup(
    name="name_of_your_module",
    version="1.0", # an example
    packages=find_packages(), # example
    install_requires=['torch', 'numpy', 'matplotlib'] # requirement
)



```

# register

In the root directory of the module, run:

```bash

pip install -e . # note the dot, it stands for "current directory"

```


End