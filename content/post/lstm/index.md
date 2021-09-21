---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "LSTM"
subtitle: ""
summary: "The most popular RNN"
authors: 
- Dylan Yang
tags: 
- Recurrent Neural Network
categories: []
date: 2021-09-20T20:06:34-04:00
lastmod: 2021-09-20T20:06:34-04:00
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

# Main idea

The key to LSTM is the `cell state`, it run stright down the entire chain, with minor linear interactions. It's built for information to flow along.

![](https://cdn.mathpix.com/snip/images/HGIUuLgyaEt9tFfWIiZ5OSBU6BV00tg1zucASVtqdhA.original.fullsize.png)

Other state is adding or removing information to the `cell state`, regulated by `gates`.
- `gates` are the comb of : 
  - signmoid, 
    - combo of 0(no info flow in) to 1, decide how much of info should let through
  - FCN, 
  - pointwise multiplcation


# Step by Step


## Forget old memory?

1st, decide if we should forget the old memory, using the `forget gate`.


![](https://cdn.mathpix.com/snip/images/GZFXBakLJVo8Q1fPPwEFNXJRS8O_qP0wIsHJqLaWEQE.original.fullsize.png)


## prepare new memory


two thing to be decided:
- which location should be updated
- the value of update

thus, we need 2 states to did the jobs.

1. the `input gate`, decides which value to update.
2. the `tanh layer` creates candidate value $\tilde{C}_{t}$


![](https://cdn.mathpix.com/snip/images/KMF0FXwYgLrb1hWGsohyrZ8mN4onc7FKADS10PsnYRI.original.fullsize.png)

## update old memory
Now, update our memory from the old one $C_{t-1}$ to the new $C_{t}$:

![](https://cdn.mathpix.com/snip/images/KrLcMwUpdu0SH0Z686CNcVzqHDhh7O-DFTXuk2xiZAE.original.fullsize.png)
1. multiply by the "forget vector", decide what to forget
2. plus $i_{t} * \tilde{C}_{t}$, the new memory


## Output

Finally, decide what to output, which is based on the cell state.(but will be a manipulated version)

Similariy:
1. decide what part to ouput
2. filtering info through tanh layer

![](https://cdn.mathpix.com/snip/images/9qE3pwnQKtwmjf48veFlla11gd6amYpARvyfPg3QorA.original.fullsize.png)


# Some variation

## Only add new info to where to forge

![](https://cdn.mathpix.com/snip/images/h_MB6OCA0DD4QAr12xyWTyXAbNM1Aid5Vc-5RfPojJ4.original.fullsize.png)

## GRU

It's simplier than LSTM (means less param)
1. combines `forget gate` and `input gate` into a single `update gate`
2. merge the `hidden state` and `cell state` into one 

![](https://cdn.mathpix.com/snip/images/-GWwZypC6BD_5lml_OYI0lNWqvavHxzUt4TMgwNdfC0.original.fullsize.png)


---
reference: https://colah.github.io/posts/2015-08-Understanding-LSTMs/


End
