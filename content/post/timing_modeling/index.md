---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Where should you model timing?"
subtitle: ""
summary: "Where you model timing effect Training and Prediction Steps"
authors: 
- Dylan Yang
tags: 
- Point Process
categories: []
date: 2021-09-25T20:36:02-04:00
lastmod: 2021-09-25T20:36:02-04:00
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

Compare (Mei and Eisner, 2017) and (Du et al. 2016)
- Mei: timing is modeled inside $h$
- Du: timing is modeled outside $h$


---

In short:

**where to model timing will have significant impact of training and prediction steps.**

That is because:
1. likelihood (training)
2. expectation (prediction)

all concludes integral(on time) of pdf.

---

# Training

$h(t)$

first, as Mei, who model the timing into $h$ ($h$ is a function of $t$). He make the memory cell of LSTM to change constantly with time:

![](https://cdn.mathpix.com/snip/images/d6v_mE7pSk0gyDAYmXwj-xjLvrtHbv59fMW1o9l3-vk.original.fullsize.png)

That is saying: $t$ is modeled into a very complex black box: LSTM(no way to do analytical analysis to $h$). Then, $h$ is a function of $t$ (an argument):

$$
\lambda_{k}(t)=f_{k}\left(\mathbf{w}_{k}^{\top} \mathbf{h}(t)\right)
$$


Since Mei use `Marked TPP`, so the likelihood is like:

$$
\ell=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\underbrace{\int_{t=0}^{T} \lambda(t) d t}_{\text {call this } \Lambda}
$$
- the 2nd term has no way to analytically compute the integral


---

$h_{j}$

Then we see Du's, he modeled timing outside $h$: 

![](https://cdn.mathpix.com/snip/images/wVjCSY6M9W7iCrIR7lEqK6JdvjPB4IM1AqzTqZNJpJI.original.fullsize.png)
- You could see $h_j$ means "the most $h$". That is saying every time the model meet an event, RNN will discretly update $h$ once. So $h$ only has a subscript "j" , to represent this $h$ is the one RNN given after $j$th event.
- Another thing is that timing is modeled in $w^{t}\left(t-t_{j}\right)$, which is very simple and independent to $h$


Since Du is also using `marked TPP`, the likelihood function will be the same. However, the integral term can be analytically solved. [see this post](https://imagoodboy.com/post/recurrent_marked_tpp/).


---

<br><br>

# Prediction

$h(t)$

If $t$ is given, then two method has no difference.

For example, you could choose the expectation of $t$:

$$\hat{t}_{i}=\mathbb{E}\left[t_{i} \mid \mathcal{H}_{i}\right]=\int_{t_{i-1}}^{\infty} t p_{i}(t) d t$$

And "type" will be determinate by $\lambda_{k}\left(t_{i}\right) / \lambda\left(t_{i}\right)$.

<br><br>


However, the most likely next event type without knowledge of $t_i$ will be:

$$\hat{k}_{i}=\operatorname{argmax}_{k} \int_{t_{i-1}}^{\infty} \frac{\lambda_{k}(t)}{\lambda(t)} p_{i}(t) d t$$
- which has an integral term. Thus Mei can only estimate the integral by some numerical tecniques such as Monte Carlo sampling.

---

$h_{j}$

For Du, even though to predict type of event, since $h$ is pre-determined before any possible $t$. So you can get $\lambda$ out of the integral:

$$\hat{k}_{i}=\operatorname{argmax}_{k} \int_{t_{i-1}}^{\infty} \frac{\lambda_{k}(t)}{\lambda(t)} p_{i}(t) d t =\frac{\lambda_{k}(t)}{\lambda(t)} \int_{t_i-1}^\infty p_i(t)dt $$

The $t$ will never effect the rank of possile $k$! So in Du's case, no matter if you want to decide $t$ in the first place or not. The prediction of $k$ will always be the same. Simply use $\lambda_{k}\left(t_{i}\right) / \lambda\left(t_{i}\right)$.
- by the way, $\lambda(t)=\sum_{k=1}^{K} \lambda_{k}(t)$.

---




End