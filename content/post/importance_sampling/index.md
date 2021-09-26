---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Monte Carlo Integration: Importance Sampling"
subtitle: ""
summary: "it's more than a variance reduction method..."
authors: 
- Dylan Yang
tags: 
- Statistics
categories: []
date: 2021-09-25T22:32:21-04:00
lastmod: 2021-09-25T22:32:21-04:00
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

simulating more samples in the importance area leads variance reduction compared to tradional Monte Carlo integration which simulate from a uniform distribution.

---

<br><br>

# Implementation

First, let me introduce the intuition behind importance sampling.


---
Here is an example of imaging processing:

![](https://cdn.mathpix.com/snip/images/5qUk_4vKOEKYqObdjbSLlvQ78ro44Sx6BJcy_HiIomw.original.fullsize.png)

Here is a hourse(may be not). We are tying to predict the shadown under its feet. Assume the shadow follows some distribution $S$, then the pdf may looks like this(since the shadow will only exist near the feet):

![](https://cdn.mathpix.com/snip/images/AS6Dyu6kyjTK8jAnB0FxPifQbndLEJTwWvNKFW2gunY.original.fullsize.png)


If the use Standard Monto Carlo Sampling to sample rays over the hemisphere(which use a uniform distribution). The shadow will have a lot variance (left).

However, if we get more samples from the interesting area (location near the feet of the hourse), we can get right with the same sampling size! (much more effcient)

![](https://cdn.mathpix.com/snip/images/UgV9cWE48npDpwwGsoCsSHZH9gznM1t5EGoBzbCkgo8.original.fullsize.png)

---

Summarize! since naive Monte Carlo use Uniform distribution. We have sample chance to explore every region of the distribution. However, in fact, Some region is more important than others.

For example, certain values of the input random variables in a simulation may have more impact on the parameter being estimated than others.

**So, we want to get some samples from the "interesting or important" region.**

**We can achieve this by sampling from a distribution that over- weights the important region.** (Having oversampled the important region, we have to adjust our estimate somehow to account for having sampled from this other distribution.)

---

<br><br>

# A Math Example

Suppose we are finding the tail probability

$$\begin{aligned} \operatorname{Pr}[X \geq \gamma] &=\int_{\gamma}^{\infty} f_{X}(x) d x \\ &=p_{\gamma} \end{aligned}$$
- it has an integral, and has no analytical solution

![](https://cdn.mathpix.com/snip/images/Ezrn1CZAaQi4wxTW9ZiR_zFe5h_vZpL1-YpRnaHuUTk.original.fullsize.png)

---

So, we could try Monte Carlo Integration.

Monte Carlo Integration 
1. samples several realizations of the random variable
2. see what percentage of the trails are in the tail beyond the threshold $\gamma$

$$\widehat{p}_{\gamma}=\frac{1}{K} \sum_{k=1}^{K} u\left(X_{k}-\gamma\right)$$
- count the value larger than $\gamma$ and divide by the number of trail $K$


The logic behind it is that the event "a trail fall into the integral interval" follows a binominal distribution which the successful rate is equal to $p_{\gamma}$

Following the property of binominal distribution, we could easily get:

This estimator is an unbiased estimator that:

$$E\left[\widehat{p}_{\gamma}\right]=p_{\gamma}$$

The variance:

$$\operatorname{var}\left[\widehat{p}_{\gamma}\right]=\frac{p_{\gamma}\left(1-p_{\gamma}\right)}{K}$$


However, how efficient is this algorithm? (how many trails we need to reach a certain level of variance?)

![](https://cdn.mathpix.com/snip/images/WAmRPz6WCBrPPdCg58qQOrxNbj3OWlpOaT-uilSlqR8.original.fullsize.png)
- level of variance is a function of true tail probability
  - when  true tail probability goes small(left), the number of trail increase
- the plot is incorrect.(inversed)

---

<br><br>

## Importance Sampling 


![](https://cdn.mathpix.com/snip/images/a_DMFacC7gz79001Z0Zg1HjIamni5h4a3N8aWPaX_vk.original.fullsize.png)

$$\begin{aligned} \operatorname{Pr}[X \geq \gamma] &=\int_{\gamma}^{\infty} f_{X}(x) d x \\ &=\int_{\gamma}^{\infty} \frac{f_{X}(x)}{f_{Y}(x)} f_{Y}(x) d x \\ &=p_{\gamma} \end{aligned}$$
- adjust the estimate so the result still equal to desired probability.


The estimator for $p_\gamma$ is 

$$
\widehat{p}_{\gamma}=\frac{1}{K} \sum_{k=1}^{K} \frac{u\left(Y_{k}-\gamma\right) f_{X}\left(Y_{k}\right)}{f_{Y}\left(Y_{k}\right)}
$$
- u is a filter that counting the number lies on the green area.


Variance follows variance average equation:

$$
\operatorname{var}\left[\widehat{p}_{\gamma}\right]=\frac{\operatorname{var}\left[\frac{u(Y-\gamma) f_{X}(Y)}{f_{Y}(Y)}\right]}{K}
$$

---

<br><br>

## Gaussian Example

assume the blue distri and the red are gaussian with unit variance:

$$
\begin{array}{l}
X \simeq \mathcal{N}(0,1) \\
Y \simeq \mathcal{N}(\gamma, 1)
\end{array}
$$

since importance sampling is a variance reduction method, we focus on the variance term:$\operatorname{var}\left[\widehat{p}_{\gamma}\right]=\frac{\operatorname{var}\left[\frac{u(Y-\gamma) f_{X}(Y)}{f_{Y}(Y)}\right]}{K}$


Recall that $Var(.) = (2nd-moment) - (1st-moment)^2$.

<br><br>

For the $(1st-moment)^2$: 

the $(1st-moment)$ is $\int_{\gamma}^{\infty} \frac{f_{X}(x)}{f_{Y}(x)} f_{Y}(x) d x$ which equals to the true tail probability $p_\gamma$.

So:

$$(1st-moment)^2 = p_\gamma^2$$

<br><br>

For (2nd-moment):

$$\frac{f_{X}(y)}{f_{Y}(y)}=\frac{\frac{1}{\sqrt{2 \pi}} e^{-\frac{1}{2} y^{2}}}{\frac{1}{\sqrt{2 \pi}} e^{-\frac{1}{2}(y-\gamma)^{2}}}$$

square and cancel common terms and square the ratio:

$$\left[\frac{f_{X}(y)}{f_{Y}(y)}\right]^{2}=\frac{e^{-y^{2}}}{e^{-(y-\gamma)^{2}}}$$


then integral over $f_Y$:

$\begin{aligned} \int_{\gamma}^{\infty}\left[\frac{f_{X}(y)}{f_{Y}(y)}\right]^{2} f_{Y}(y) d y &=\int_{\gamma}^{\infty} \frac{e^{-y^{2}}}{e^{-(y-\gamma)^{2}}} \frac{1}{\sqrt{2 \pi}} e^{-\frac{1}{2}(y-\gamma)^{2}} d x \\ &=e^{\gamma^{2}} \int_{\gamma}^{\infty} \frac{1}{\sqrt{2 \pi}} e^{-\frac{1}{2}(y+\gamma)^{2}} d x \end{aligned}$
- 1st term: $e^{\gamma^{2}}$
- 2nd term: the pdf of a $N(-\gamma, 1)$


$\operatorname{var}\left[\frac{u(Y-\gamma) f_{X}(Y)}{f_{Y}(Y)}\right]=e^{\gamma^{2}} \int_{\gamma}^{\infty} \frac{1}{\sqrt{2 \pi}} e^{-\frac{1}{2}(y+\gamma)^{2}} d x - p_\gamma^2$

---

The experiment:

![](https://cdn.mathpix.com/snip/images/6d3fVLd1KNqHMvB8GKDyp0tFI2Bx5IUak7DJSK5sCL0.original.fullsize.png)



<br><br>

# the general case

if we are trying to find $\mathbb{E}(f(\boldsymbol{X}))=\int_{\mathcal{D}} f(\boldsymbol{x}) p(\boldsymbol{x}) \mathrm{d} \boldsymbol{x}$.

given a new distribution $q$ has larger density on the interesting area:

$$\mu=\int_{\mathcal{D}} f(\boldsymbol{x}) p(\boldsymbol{x}) \mathrm{d} \boldsymbol{x}=\int_{\mathcal{D}} \frac{f(\boldsymbol{x}) p(\boldsymbol{x})}{q(\boldsymbol{x})} q(\boldsymbol{x}) \mathrm{d} \boldsymbol{x}=\mathbb{E}_{q}\left(\frac{f(\boldsymbol{X}) p(\boldsymbol{X})}{q(\boldsymbol{X})}\right)$$


Then the "importance sampling version" of estimate of $\mu=\mathbb{E}_{p}(f(\boldsymbol{X}))$ is:

$$\hat{\mu}_{q}=\frac{1}{n} \sum_{i=1}^{n} \frac{f\left(\boldsymbol{X}_{i}\right) p\left(\boldsymbol{X}_{i}\right)}{q\left(\boldsymbol{X}_{i}\right)}, \quad \boldsymbol{X}_{i} \sim q$$


And the variance of estimate:

$$\operatorname{Var}_{q}\left(\hat{\mu}_{q}\right)=\frac{1}{n}\left[\int_{\mathcal{Q}}\left(\frac{f(\boldsymbol{x}) p(\boldsymbol{x})}{q(\boldsymbol{x})}\right)^{2} q(\boldsymbol{x}) \mathrm{d} \boldsymbol{x}-\mu^{2}\right]$$


---

End


Reference

- [Timothy Schulz](https://www.youtube.com/watch?v=pAbQHKr_Zqo)
- [Art Owen](https://statweb.stanford.edu/~owen/)

---