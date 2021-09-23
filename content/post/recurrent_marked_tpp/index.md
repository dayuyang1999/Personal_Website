---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Recurrent Marked TPP"
subtitle: ""
summary: ""
authors: 
- Dylan Yang
tags: 
- Statistics
- Point Process
categories: []
date: 2021-09-23T13:29:35-04:00
lastmod: 2021-09-23T13:29:35-04:00
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





# Problem Formulation

A set of sequence $C={S^1, S^2, ...}$, each sequence  $S^i=((t^i_1, y^i_1), (t^i_2, y^i_2), ...)$
- t = happen time
- y = marker (type of event)


<br><br>
<br><br>


# Model: RECURRENT MARKED TEMPORAL POINT PROCESS

<br><br>

## Probability

Given the history of past events, we can explicitly specify the density funciton of the next event, (happen at time t with type y):

$$
f^{*}(t, y)=f\left(t, y \mid \mathcal{H}_{t}\right)
$$


Then the likelihood of a sequence is:

$$
f\left(\left\{\left(t_{j}, y_{j}\right)\right\}_{j=1}^{n}\right)=\prod_{j} f\left(t_{j}, y_{j} \mid \mathcal{H}_{t}\right)=\prod_{j} f^{*}\left(t_{j}, y_{j}\right)
$$

the pdf $f^*$ coudl have many design. One possible way is to seperate `type` and `time`:

$$
f\left(t_{j}, y_{j} \mid \mathcal{H}_{t}\right)=f(y_j|t_j, H) f(t_j|H)
$$
- means, first decide timing, and then type.
- $f(y_j|t_j)$ is a multinominal distribution


{{% callout note %}}

if $f(t_j|H) = f\left(t_{j} \mid \ldots, t_{j-2}, t_{j-1}\right)$
- then $f(t_j)$ cannot capture the influence of past 

{{% /callout %}}



<br><br>


## Model Formulation


Almost all TPP-based research, use a particular funcitonal form of CIF $\lambda$. to capture some phenomena of interests.

Idea: RNN to model nolinear dependency btw timing and type.(make both of them as input, the output hidden state as the representation of nonlinear dependency)


![](https://cdn.mathpix.com/snip/images/mDeLDIl-GU7m6g10NxGR0uf1S17uhDAoMf4ziNIxEKQ.original.fullsize.png)
- when meeting a new event $(t_j, y_j)$, a one recurrence
- finally $h_j$ summarize the influence of the history.

So, the pdf of t(of the next incoming event) could be conditioned on $h_j$:

$$f^{*}\left(t_{j+1}\right)=f\left(t_{j+1} \mid \mathcal{H}_{t}\right)=f\left(t_{j+1} \mid \boldsymbol{h}_{j}\right)=f\left(d_{j+1} \mid \boldsymbol{h}_{j}\right)$$
- $d_{j+1}=t_{j+1}-t_{j}$

{{% callout note %}}


Since $h_j$ summarized "the influence of history".

So we do not need to specify a fixed parametric formulation for describing the dependency of the history, like what traditional hawkes process did in the 2nd part of the $\lambda$:

$$
\lambda^{*}(t)=\gamma_{0}+\alpha \sum_{t_{j}<t} \gamma\left(t, t_{j}\right)
$$

{{% /callout %}}



![](https://cdn.mathpix.com/snip/images/TCMkHCHc07ST8T6nJ_ZdUBHsjEkSbV6SG_1W2XapQJI.original.fullsize.png)


### Input Layer

$y$, marker: one-hot, pass into a FCN

$t$ no change

### Hidden layer

since it's simple RNN:

$$
\boldsymbol{h}_{j}=\max \left\{\boldsymbol{W}^{y} \boldsymbol{y}_{j}+\boldsymbol{W}^{t} \boldsymbol{t}_{j}+\boldsymbol{W}^{h} \boldsymbol{h}_{j-1}+\boldsymbol{b}_{h}, 0\right\}
$$

### Maker Generation

The probability of a type, is a function of $h_j$

$$P\left(y_{j+1}=k \mid \boldsymbol{h}_{j}\right)=\frac{\exp \left(\boldsymbol{V}_{k,:}^{y} \boldsymbol{h}_{j}+b_{k}^{y}\right)}{\sum_{k=1}^{K} \exp \left(\boldsymbol{V}_{k,:}^{y} \boldsymbol{h}_{j}+b_{k}^{y}\right)}$$

very common in ML papers...



{{% callout note %}}

In this paper, the timing and the tyoe are assumed as independent with each other. So the joint density is simply the product of two.

$$f\left(t_{j}, y_{j} \mid \mathcal{H}_{t}\right)=f\left(y_{j}\right) f\left(t_{j} \mid \ldots, t_{j-2}, t_{j-1}\right)$$


That means when generate a new event(or, called prediction).

`generate type`, and `generate time` are seperate processes.


{{% /callout %}}




### Time generation

recall the pdf of $t$:

$$f^{*}(t)=\lambda^{*}(t) \exp \left(-\int_{t_{j}}^{t} \lambda^{*}(\tau) d \tau\right)$$


to estimate the timing of next incoming event, using the expectation: (**why use expectation?**)

$$
\hat{t}_{j+1}=\int_{t_{j}}^{\infty} t \cdot f^{*}(t) d t
$$


{{% callout note %}}

if I combo these two together, the estimation of timing is 

$$
\hat{t}_{j+1} = \int_{t_{j}}^{\infty} t\cdot \left[   \lambda^{*}(t) \exp \left(-\int_{t_{j}}^{t} \lambda^{*}(\tau) d \tau\right) \right]dt
$$

Notice that: here **the double-integral is all targeting time t**.

---

If we want to have an analytic solution of $\hat{t}_{j+1}$, what kind of parameterization of $\lambda$ should we have?

- **we cannot integrate the modeling of timing into a complex structure.**

Since this idea is so important, here I give 2 concrete examples(particularly pay attention to timing):

---
1st, What (Mei and Eisner 2017) did: 


$$
\lambda_{k}(t)=f_{k}\left(\mathbf{w}_{k}^{\top} \mathbf{h}(t)\right)
$$
- $h$ is a function of t (continous)

$$
\mathbf{h}(t)=\mathbf{o}_{i} \odot(2 \sigma(2 \mathbf{c}(t))-1) \text { for } t \in\left(t_{i-1}, t_{i}\right]
$$
- $c$ is a function of t (continous):

$$
\mathbf{c}(t) \stackrel{\text { def }}{=} \overline{\mathbf{c}}_{i+1}+\left(\mathbf{c}_{i+1}-\overline{\mathbf{c}}_{i+1}\right) \exp \left(-\boldsymbol{\delta}_{i+1}\left(t-t_{i}\right)\right) \text { for } t \in\left(t_{i}, t_{i+1}\right]
$$

Here we could see that: (Mei and Eisner 2017) integrate the modeling of timing into a complex Deep Learning Structure(LSTM). So when he estimate the expectation of $t$, he has no way to derive the analytic solution, and must use numerical method to handle all the integrals.


---
2nd, what (Du et al. 2016) did:

$$
\lambda^{*}(t)=\exp (\underbrace{\boldsymbol{v}^{t^{\top}} \cdot \boldsymbol{h}_{j}}_{\begin{array}{c}
\text { past } \\
\text { influence }
\end{array}}+\underbrace{w^{t}\left(t-t_{j}\right)}_{\begin{array}{c}
\text { current } \\
\text { influence }
\end{array}}+\underbrace{b^{t}}_{\begin{array}{c}
\text { base } \\
\text { intensity }
\end{array}})
$$







{{% /callout %}}




