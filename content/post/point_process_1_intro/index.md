---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "TPP intro: PDF, CDF, Notations and Likelihood"
subtitle: ""
summary: "Point Process is widely used in modeling sequence of events that: 1. timing is important 2.rare to happen"
authors: 
- Dylan Yang
tags: 
- Statistics
- Point Process
categories: []
date: 2021-09-17T20:51:00-04:00
lastmod: 2021-09-17T20:51:00-04:00
featured: false
draft: false
math: true

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

- [Survival Function](#survival-function)
- [The Hazard Function](#the-hazard-function)
- [The Relationship bewteen notations](#the-relationship-bewteen-notations)
- [Summary of the meaning of each notation](#summary-of-the-meaning-of-each-notation)
  - [$\lambda$:](#lambda)
  - [f(t)](#ft)
  - [$S(t)$](#st)
- [If we have multiple type of event K](#if-we-have-multiple-type-of-event-k)
- [Some Other Useage](#some-other-useage)
  - [Expectation of Life](#expectation-of-life)




To comfortably introduce Hawkes Process, first we have to understand some idea in Survival Analysis in Statistics.

<br><br>
<br><br>

# Survival Function

Assume T is a continuous random variable, it may represent "Length of Survival Time" of a patient.

Then, "the probability that this patient could live longer than t" is defined as `Survival Function` $S$:

$$
S(t)=\operatorname{Pr}\{T \geq t\}=1-F(t)=\int\_{t}^{\infty} f(x) d x
$$

<br><br>

# The Hazard Function

Then, the patient still survive at time point t, but he is still in danger of, say, heart failure. How could we quantify the "seriousness" of his condition? 

We define the instantaneous rate of occurrence of the event as `hazard function`:

$$
\lambda(t)=\lim \_{d t \rightarrow 0} \frac{\operatorname{Pr}\{t \leq T<t+d t \mid T \geq t\}}{d t}
$$

Here is a equavalent definition:

$$
\lambda(t)=\lim \_{d t \rightarrow 0} \frac{\operatorname{Pr}{(N(t+dt)- N(t)=1)}}{d t}
$$

- $\lambda$ could be roughly seen as the *1st derivitive* of the probability
  - so itself is not a probability
  - a rate of event occurrence per unit of time






<br><br>

Recall the conditional probability formula:

$$Pr(A|B) = \frac{Pr(AB)}{Pr(B)}$$

- For the numerator, it is the joint probabiliry that $T$ is in the interval $[t,t+dt)$ and $T \ge t$ (which is the same as the probability that t is in the interval):

  - $$P(t \leq T<t+d t ,\; T \geq t) = P(t \leq T<t+d t)$$
  - When $dt$ is a small interval:
    - $$P(t \leq T<t+d t)=\int\_{t}^{t+dt} f(x)dx \approx f(t)\times dt$$


- For the denumerator:
  - $P(T \ge t) = S(t) =1-F(t)$



Overall : the harzard function could be write as:



$$\lambda(t) = \frac{\frac{f(t)dt}{1-F(t)}}{dt} = \frac{f(t)}{1-F(t)}=\frac{f(t)}{S(t)}$$

In sum: 

the rate of the **first occurrence** of the event at duration t (`harzard function`), equals the density of events at t, divided by the probability of surviving to that duration without experiencing the event.



---

Note that:


$$S'(t) = - f(t)$$

So it's able to rewrite harzard function as:

$$\lambda(t) = -\frac{d}{d t} \log S(t) $$


if we integrate from $0$ to $t$ for both side:

$$\int\_{0}^{t} \lambda(u) du = - (logS(t) - logS(0))$$

Since $S(0) =  P(T>0) = 1$, can be simplied as:

$$\int\_{0}^{t} \lambda(u) du = - logS(t)$$

---
Note:

What if now we are now just pass $t_k$, and the patient still survive. (We have already know the event did not happened during [0, t]).

We could take the integral from $t_k$ to $t$ intead of $0$ to $t$:

The result is 

$$\int\_{0}^{t} \lambda(u) du = - (logS(t) - logS(t_k))$$

Similarily, we already know there's no event happened before $t_k$, that means $S(t_k) = Pr(T>t_k)=1$

We could still remove the late term $logS(t_k)$

---

This is equavalent to:

$$S(t) = \exp\left(-\int_0^t \lambda(x)dx\right)$$


- the integral term is called `the cumulative hazard` (or cumulative risk):
  - $$\Lambda(t)=\int\_{0}^{t} \lambda(x) dx$$
    - You may think of $Λ(t)$ as the sum of the risks you face going from duration 0 to t.



We could also obtain the relation between `hazard function`  with the distribution of T:

$$
F^{*}(t)=1-\exp \left(-\int_{t_{k}}^{t} \lambda^{*}(u) \mathrm{d} u\right), \quad f^{*}(t)=\lambda^{*}(t) \exp \left(-\int_{t_{k}}^{t} \lambda^{*}(u) \mathrm{d} u\right)
$$


<br><br>

# The Relationship bewteen notations
let's be clear to the relationship between the following elements:



Define $N(t) = |h: t_h \le t|$ to be the count of events (of any type) preceding time $t$.



given the past history $H_{i}$, the number of events in $[t_{i-1}, t]$, is denoted as: $\Delta N\left(t_{i-1}, t\right) \stackrel{\text { def }}{=} N(t)-N\left(t_{i-1}\right)$.

- probability $Pr$
  - $P(\Delta N(t, t+dt)=0)$
  - $P(\Delta N(t, t+dt)=1)$
  - $P(\Delta N(T_{i-1}, T_i)=0)$
  - $P(\Delta N(T_{i-1}, T_i)=1)$
  - $P(T_i > t)$ 
  - $P(T_i < t)$
  - ...
- Intensity $\lambda$
- Cumulative risk $\Lambda$ 
- pdf $f(t)$
- cdf $F(t)$




<br><br>

Now we just pass $T_{i-1}$, and know that there's no event happen, we are try to infer if the patient will still be alive until $T_i$

There's not event before $T_i$, or no event in $[T_{i-1}, T_i]$(already know that there's no event happen before $T_{i-1}$):

$$P(\Delta N(T_{i-1}, T_i)=0) = P(T_i > t) = 1 - P(T_i \le t) = 1- F(t) = \exp(-\int_{t_{i-1}}^{t} \lambda(s) ds)$$

$$\exp\left(-\int_{t_{i-1}}^{t} \lambda(s) ds\right) = - \left( \Lambda(t) - \Lambda(t_{i-1}) \right)$$


There is some events before t, or the event happen in $[T_{i-1}, T_i]$ (already know that there's no event happen before $T_{i-1}$):

$$P(\Delta N(T_{i-1}, T_i) >0) = P(T_i \le t) = 1 - P(T_i > t) = F(t) =1 -  \exp\left(-\int_{t_{i-1}}^{t} \lambda(s) ds\right)$$



<br><br>

Here we could see that the time (**waiting time** $t-t_k$) is a random variable. And the pdf and cdf are given above. 

Let's explore more about the shape of pdf:

---

fix $t-t_k = 1$

if we have $t_k=0$, back to the `survival function` case, let's assume intensity $\lambda$ =1 :
![](https://cdn.mathpix.com/snip/images/HfoSScNLsJ6cjtjOvw3X_O3bwE6sGtLitzLEtsycvFk.original.fullsize.png)
- the pdf is just $e^{-x}$,  ($\int a e^{-ax}$ = 1)

if we have intensity $\lambda$ =2 :

![](https://cdn.mathpix.com/snip/images/otl0dRSSXNBZ-ASPb2MO3oupXHzndkIv0iAu889_HiE.original.fullsize.png)
- decay more intensively (since the $\int$ always =1, and we start higher)

---

<br><br>



# Summary of the meaning of each notation

## $\lambda$: 
- "the number of event happen per unit time"

or $\lambda dt$ is the probability of the occurence of a new event.(Givne the history $H$)

## f(t)


$$f^{*}(t)=\lambda^{*}(t) \exp \left(-\int_{t_{n}}^{t} \lambda^{*}(\tau) d \tau\right)$$

after observing the most recent event $e_{i-1}$, for the timing of next event(not happen yet)
 $t$, $t$ is a random varible.

$f(t)$: the pdf

$F(t)$: the cdf, $P(T < t)$, the probability that the new event will hapen before time t.(since the last event timing $t_{i-1}$)


## $S(t)$

First, be clear that
$$
\lambda^{*}(t) d t=\frac{f^{*}(t) d t}{S^{*}(t)}=\frac{f^{*}(t) d t}{1-F^{*}(t)}
$$

$S^{*}(t)=\exp \left(-\int_{+}^{t} \lambda^{*}(\tau) d \tau\right)$ is the probability that the new event happen after $t$, $P(T>t)$.






# If we have multiple type of event K

If multiple types of event are implemented, there are two stage of the model:

1. decide the time
2. decide the type


Using a single point process to model the "timing": $\lambda(t)$
Then choose the type by:

$$P\left(K_{i}=k_{i} \mid t_{i}\right)=\frac{\lambda_{k_{i}}\left(t_{i}\right)}{\lambda\left(t_{i}\right)}$$

So we have the relationship:

$$\sum_k \lambda_k(t) = \lambda(t)$$

---

If we observe the sequence of differnet types of events: $\left[\left(k_{1}, t_{1}\right),\left(k_{2}, t_{2}\right), \ldots,\left(k_{T}, t_{T}\right)\right]$
- each event was decided by 1.timing 2. which type

For modeling such events sequence, the likelihood function:

![](https://cdn.mathpix.com/snip/images/LS-oBKz3SEGe1Am63huLa1tGE1NgqSEHnATuk-SD03M.original.fullsize.png)


Logize:

$$\ell \stackrel{\text { def }}{=} \log \mathcal{L}$$

$$=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\sum_{i: t_{i} \leq T}\left(\Lambda\left(t_{i}\right)-\Lambda\left(t_{i-1}\right)\right)$$

$$=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\Lambda(T)$$

$$=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\int_{t=0}^{T} \lambda(t) dt$$

$$=\sum_{i: t_{i} \leq T} \log \lambda_{k_{i}}\left(t_{i}\right)-\int_{t=0}^{T} \sum_k \lambda_k(t) d t$$







<br><br>
<br><br>

# Some Other Useage 

## Expectation of Life

$$
\mathbb{E}(T)=\int_{0}^{\infty} u f(u) d u
$$

the expectation of life could also be obtain from `survival function`:

use "integrating by part":
$$
\int u d v=u v-\int v d u
$$

the former could be write as:

$$
\begin{aligned}
\int u f(u) d u &=-\int u d S(u) \\\\
&=-\left.\left(u s(u)-\int s(u) d n\right)\right|\_{0} ^{\infty} \\\\
&=\int\_{0}^{\infty} S(u) d u
\end{aligned}
$$

<br><br>
<br><br>

The `harzard function` here is the `Conditional Intensity Function` we gonna talk later.


---

reference:
- [Survival Analysis Lecture Note](https://data.princeton.edu/wws509/notes/c7s1)
- 