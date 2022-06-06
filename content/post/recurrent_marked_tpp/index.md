---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Recurrent Marked Temporal Point Process"
subtitle: ""
summary: "The timing modeling is not embedded in the complex structure of RNN, so the pdf of $t$ could be solved analytically"
authors: 
- Dylan Yang
tags: 
- Statistics
- Point Process
- Recurrent Neural Network

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

Code: https://github.com/woshiyyya/ERPP-RMTPP

---

- [Comments](#comments)
- [Problem Formulation](#problem-formulation)
- [Model: Recurrent Marked TPP](#model-recurrent-marked-tpp)
  - [Probability](#probability)
  - [Model Formulation](#model-formulation)
    - [Input Layer](#input-layer)
    - [Hidden layer](#hidden-layer)
    - [Type Generation](#type-generation)
    - [Time generation](#time-generation)
- [Training](#training)
- [Appendix](#appendix)
  - [get the analytical solution of $\hat{t}$ under (Du et al. 2016) case.](#get-the-analytical-solution-of-hatt-under-du-et-al-2016-case)

# Comments

TPP+RNN = continous sequence dynamic modeling (key is make RNN continuous)

1. The timing modeling is not embedded in the complex structure of RNN, so the pdf of $t$ could be solved analytically. 

2. the key is $h$, RNN summarize all historical event as $h$, which replace the summation term of the CIF parameterization of traditional hawkes process. 
   1. then, use CIF $\lambda$ to decide timing(pdf), type()

# Problem Formulation

A set of sequence $C={S^1, S^2, ...}$, each sequence  $S^i=((t^i_1, y^i_1), (t^i_2, y^i_2), ...)$
- t = happen time
- y = marker (type of event)


<br><br>
<br><br>


# Model: Recurrent Marked TPP

<br><br>

## Probability

Given the history of past events, we can explicitly specify the density funciton of the next event, (happen at time t with type y):

$$
f^{*}(t, y)=f\left(t, y \mid \mathcal{H}_{t}\right)
$$


Then the likelihood of a sequence is:

![](https://cdn.mathpix.com/snip/images/jxPViR_PbAw1hoX9yGc4J1ARfzTXwH29J5-S9DFaaqo.original.fullsize.png)

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

![](https://cdn.mathpix.com/snip/images/a-6SuKjW7y7Ynpm8zKYLobxtri-bvKYa7Bawi07xH2I.original.fullsize.png)

### Type Generation

The type generation follows "How Traditional Deep Learning cope with multi-nominal distribution" idea, put into a FCN, and pass to softmax.

The probability of a type, is a function of $h_j$

$$P\left(y_{j+1}=k \mid \boldsymbol{h}_{j}\right)=\frac{\exp \left(\boldsymbol{V}_{k,:}^{y} \boldsymbol{h}_{j}+b_{k}^{y}\right)}{\sum_{k=1}^{K} \exp \left(\boldsymbol{V}_{k,:}^{y} \boldsymbol{h}_{j}+b_{k}^{y}\right)}$$




{{% callout note %}}

In this paper, the timing and the tyoe are assumed as independent with each other. So the joint density is simply the product of two.

$$f\left(t_{j}, y_{j} \mid \mathcal{H}_{t}\right)=f\left(y_{j}\right) f\left(t_{j} \mid \ldots, t_{j-2}, t_{j-1}\right)$$


That means when generate a new event(or, called prediction).

`generate type`, and `generate time` are seperate processes.


{{% /callout %}}




### Time generation

recall the pdf of $t$:

![](https://cdn.mathpix.com/snip/images/AU6BYZq39yx0BHl8lbaics-FCTYLBOEhy0RheD0Mhoo.original.fullsize.png)

to estimate the timing of next incoming event, using the expectation: (**why use expectation?**)

$$\hat{t}_{j+1}=\int_{t_{j}}^{\infty} t \cdot f^{*}(t) dt$$


---

$\hat{t}_{j+1}$ is relied on CIF, and it's defined as:

![](https://cdn.mathpix.com/snip/images/Wq8-QpYPte9aaJuSxxmvgW-5dBB89A_gzKp0NbMEFAY.original.fullsize.png)




{{% callout note %}}

if I combo these two together, the estimation of timing is 

![](https://cdn.mathpix.com/snip/images/vh3NG0I7u23yKVbhOchmdIfdFaq_c4nmAg0V54zPYWY.original.fullsize.png)

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

![](https://cdn.mathpix.com/snip/images/ul-sMtDv-szMCYPTtMQilF5qwEB0fIbjmP8Q49EMB7I.original.fullsize.png)
- $c$ is a function of t (continous):
![](https://cdn.mathpix.com/snip/images/OhkFYllLJCxhhewAvJGqjT4cY00JTQGuNGJV8BVfmX0.original.fullsize.png)

Here we could see that: (Mei and Eisner 2017) integrate the modeling of timing into a complex Deep Learning Structure(LSTM). So when he estimate the expectation of $t$, he has no way to derive the analytic solution, and must use numerical method to handle all the integrals.


---
2nd, what (Du et al. 2016) did:
![](https://cdn.mathpix.com/snip/images/x0meaLBzNNvUPdbrIGVmx_xZpj-CLQz9cu-I2w2MDFg.original.fullsize.png)
- $h_j$ is updated when the most recent event happens(and then never changed). It's unrelated to $t$. (So could be seen as a constant)
- timing is fully modeled in $w^{t}\left(t-t_{j}\right)$. 


So, since except $w^{t}\left(t-t_{j}\right)$, the rest of the terms are unrelated to $t$ (treat as constant when doing integral), so, for convinience, I rewrite $\lambda$ as:

$$\lambda^*(t) = e^{at+b}$$
- a = w (a scalar)
- b = the rest, containing $h$ which is the output of "a complex structure"


The analytic solution is obviously obtainable. see Appendix for derivation.


---




{{% /callout %}}


<br><br>


# Training

MLE:

$$\ell\left(\left\{\mathcal{S}^{i}\right\}\right)=\sum_{i} \sum_{j}\left(\log P\left(y_{j+1}^{i} \mid \boldsymbol{h}_{j}\right)+\log f\left(d_{j+1}^{i} \mid \boldsymbol{h}_{j}\right)\right)$$




<br><br>











<br><br>
<br><br>







# Appendix

## get the analytical solution of $\hat{t}$ under (Du et al. 2016) case.

According to:

$$\hat{t}_{j+1} = \int_{t_{j}}^{\infty} t\cdot \left[   \lambda^{*}(t) \exp \left(-\int_{t_{j}}^{t} \lambda^{*}(\tau) d \tau\right) \right]dt$$

if we are trying to analytically analysis $\hat{t}$ above:

$$\int x(a x+b)\left(e^{a x+b}-e^{at_j-b}\right) d x$$
- since just a trail, I set $t_j=0$ for simplicity:

reset $u=a x+b$:

$$=\int \frac{u(u-b)\left(e^{u}-e^{b}\right)}{a^{2}} d u$$

take the constant out: 

$$=\frac{1}{a^{2}} \cdot \int u(u-b)\left(e^{u}-e^{b}\right) d u$$

expand $u(u-b)\left(e^{u}-e^{b}\right): e^{u} u^{2}-e^{b} u^{2}-b e^{u} u+b e^{b} u$:

$$=\frac{1}{a^{2}} \cdot \int e^{u} u^{2}-e^{b} u^{2}-b e^{u} u+b e^{b} u d u$$

summation rule, we could integral each tiny part seperately:

$$=\frac{1}{a^{2}}\left(\int e^{u} u^{2} d u-\int e^{b} u^{2} d u-\int b e^{u} u d u+\int b e^{b} u d u\right)$$

---
1st part: $\int e^{u} u^{2} d u=u^{2} e^{u}-2\left(e^{u} u-e^{u}\right)$

2nd part: $\int e^{b} u^{2} d u=\frac{e^{b} u^{3}}{3}$

3rd part: $\int b e^{u} u d u=b\left(e^{u} u-e^{u}\right)$

4th part: $\int b e^{b} u d u=\frac{b e^{b} u^{2}}{2}$

---

so:

$$=\frac{1}{a^{2}}\left(u^{2} e^{u}-2\left(e^{u} u-e^{u}\right)-\frac{e^{b} u^{3}}{3}-b\left(e^{u} u-e^{u}\right)+\frac{b e^{b} u^{2}}{2}\right)$$

and reverse change varibale: $u=a x+b$

$$=\frac{1}{a^{2}}((a x+b)^{2} e^{a x+b}-2\left(e^{a x+b}(a x+b)-e^{a x+b}\right)-\frac{e^{b}(a x+b)^{3}}{3}-b(e^{a x+b}(a x+b)-e^{a x+b}+\frac{b e^{b}(a x+b)^{2}}{2})$$

