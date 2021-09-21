---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Temporal Point Process 3.1: The Neural Hawkes Process"
subtitle: "The key is to be clear about: 1. Parameterize $\\lambda$, 2. Training, 3. Prediction and 4. Evaluation: "
summary: ""
authors: 
- Dylan Yang
tags: 
- Statistics
- Recurrent Neural Network
categories: []
date: 2021-09-20T11:53:27-04:00
lastmod: 2021-09-20T11:53:27-04:00
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

By observing $\left[\left(k_{1}, t_{1}\right),\left(k_{2}, t_{2}\right), \ldots,\left(k_{T}, t_{T}\right)\right]$ sequence as training data, how to train the point process model? How could we predict? How to tell if the prediction is reliable?

For parameterizing step:


For training step, should be clear:
- what is the parameter to learn
- what is the likelihood function 
- the algorithm enables learning

For prediction:
- what is the algorithm

For evaluation:
- what is the metrics.




# Problem Description
Modeling K types of events, each of which are observed to occur in continuous time.

observed event streams $\left(k_{1}, t_{1}\right),\left(k_{2}, t_{2}\right), \ldots$
- $k_{i} \in\{1,2, \ldots, K\}$ is an event type
- $0<t_{1}<t_{2}<\cdots$ are times of occurrence





# Parameterizing $\lambda$: Continus LSTM hidden state


The base idea: apply the `hidden state`, and transform it to $\lamdba_k(t)$

2 issues let the famous [LSTM](https://imagoodboy.com/post/lstm/) does not applicable to the observed data:

1. The LSTM we are familar with is the discrete version. However, $\lambda(t)$ is time dependency. 
2. The LSTM has an "input vector"
3. the observed data has type of event info "K"

So, the author make the following edit to make LSTM applicable:

---
**time dependicy**

adding an exponential decay factor to "updating Cell state step"

let the value is bounded between $[C_{t_{i-1}}, C_{t_i}]$. Apply the decay factor to the new information $\tilde{C_t}$. And follow:
- when the $t-t_i\rightarrow 0$ (time decay factor = 1),  $C_{t_{i}}\rightarrow C_{t_i}$
- when the $t-t_i\rightarrow \infty$ (time decay factor = 0), $C_{t_{i}}\rightarrow C_{t_i}$

---
**type as input**

make event type as a one-hot encoding, as input

---



Now, the `hidden state` $h(t)$ is
- dependent on a time-varying $C(t)$ (so itself is time-varying)
- has $.._k$  subscript

<br><br>
<br><br>


# Training Algorithm


## MLE

borrow from general point process, likelihood function:

$$\ell=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\underbrace{\int_{t=0}^{T} \lambda(t) d t}_{\text {call this } \Lambda}$$
- front: the sum of log-intensity, for those time that the events happened (should be A large AP)
- end: the integral of the total intensity over the whole observation window (should be A small AP)
  


### The integral

Using Monte-Carlo Integration to handle the integral part of the likelihood (it has no analytical solution). Which gives an unbiased estimat of $\Lambda$

![](https://cdn.mathpix.com/snip/images/yuq8GwJOMFOesoCspIwYanrcfEAIyHaTBmEdcoBPGmc.original.fullsize.png)
- the key is to uniformly draw sample from the entire time integral interval.

### Optimize

using SGD to max likelihood



# Prediction

**Prediction task:**

Given $\left(k_{1}, t_{1}\right),\left(k_{2}, t_{2}\right), \ldots,\left(k_{i-1}, t_{i-1}\right)$

Predict:
- time
- type

## Time

**timing is a random variable!**

we already know the distribution of $(t_i = t \mid H_i)$ is: 
$$f(t|H_i) = \lambda(t) \exp \left(-\int_{t_{i-1}}^{t} \lambda(s) d s\right)$$
- you may interpret it as "the patient survive to t_i, given that we have already known the patien survive through $t_{i-1}$", detail see the [intro](https://imagoodboy.com/post/point_process_1_intro/).


to predict a single time, and expect L2 loss to be as low as possible, we should choose:

$$\hat{t}_{i}=\mathbb{E}\left[t_{i} \mid \mathcal{H}_{i}\right]=\int_{t_{i-1}}^{\infty} t p_{i}(t) d t$$
- the expectation of $t$


## Type

after fix the time, we could compute $\lambda_{k}\left(\hat{t_{i}}\right) ,  \lambda\left(\hat{t_{i}}\right)$, we choose the one gives maximum 

recall $E(f(x)) = \int f(x) p(x) dx$, the fraction $\frac{\lambda_{k}\left(\hat{t_{i}}\right)}{\lambda\left(\hat{t_{i}}\right)}$ is a function of t, then we could compute $\int_{t_{i-1}}^{\infty} \frac{\lambda_{k}(t)}{\lambda(t)} p_{i}(t) d t$ for all choice of k, and choose the largest $k_i$

$$ argmax_k \frac{\lambda_{k}\left(\hat{t_{i}}\right)}{\lambda\left(\hat{t_{i}}\right)}$$


The integral is estimated by Monte Carlo Sampling.



# Simulation

draw radndom sequence from the model, use `thinning algorithm`


The current situation:

we has observed the first $i-1$ events. And we need to generate $i$ event.

There are $K$ types of event, so which one in $K$ should we pick up?
- follow the "earlist rule" 

---

**the earlist rule**

from the last update of continuous LSTM, we get totally k $\lambda_{k}^{i}$ for the $i$th event.


Then we simulate each k event with k non-homogeneous Poisson process over $\left(t_{i-1}, \infty\right)$ whose intensity function is $\lambda_{k}^{i}$.
- since $\lambda$ is varying according to i(time indicator), so this is a "non-homogeneous Poisson process".

Then we take the event happens eariliest, as $(k_i, t_i)$

---


## Thinning Algorithm

However, draw sample from non-homogeneous Poisson process is inefficient. we could draw each k types of event with k homogeneous Possion process seperately.

Here is a picture describing the simulation process:


![](https://cdn.mathpix.com/snip/images/yCqwaVKM-hvVhmk7vNIToK2CRfVbsadJY115fGjTyLI.original.fullsize.png)


**left**

- gold line = value of $\lambda^*$ (so its a homogeneous one)
- gold squares = samples of type-1 events 
- purple line = $\lambda_{1}^{i}(t)$


**mid**
- radnomly accept sample with probability  $\frac{\lambda_{1}^{i}(t)}{ \lambda^{*}}$.

**right**
- green pentagons =  repeat for type=2 event


**finally**:
decide the next event:
- The next event is the earliest one among these surviving candidates.

<br><br>

Then, formally define the algorithm

![](https://cdn.mathpix.com/snip/images/kwP_A6jKYVPblY7RDNAPngh4Afxbx8o2TCoDcL8wllc.original.fullsize.png)


## $\lambda^*$

how do we construct the upper bound $\lambda^{*}$ on $\lambda_{k}^{i}$?






