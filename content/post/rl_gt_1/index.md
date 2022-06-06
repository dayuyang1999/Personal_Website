---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Finite MDPs"
subtitle: "Finite Markov Decision Processes"
summary: "Notes from txtbook Chapter 3"
authors: []
tags: 
- Reinforcement Learning
categories: []
date: 2022-01-11T16:43:58-05:00
lastmod: 2022-01-11T16:43:58-05:00
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

- [Basic Settings](#basic-settings)
- [Dyanmics of MDPs](#dyanmics-of-mdps)
  - [Properties of $p$](#properties-of-p)
- [Reward](#reward)
- [Policies and Value Func](#policies-and-value-func)
  - [Value function of state (state value func)](#value-function-of-state-state-value-func)
  - [Value function of action (action value func)](#value-function-of-action-action-value-func)
  - [Bullman Equation](#bullman-equation)
    - [1. What is $E_{\pi}$?](#1-what-is-e_pi)
    - [2. what is the rest: $E_{\pi}\left(\gamma G_{t+1} \mid s_{1}=s\right)$ ?](#2-what-is-the-rest-e_pileftgamma-g_t1-mid-s_1sright-)
    - [3. overall](#3-overall)
    - [4. meaning](#4-meaning)

# Basic Settings

- State
- Action
- Reward (subscript +1)

the sequence is like:

$$S_{0}, A_{0}, R_{1}, S_{1}, A_{1}, R_{2}, S_{2}, A_{2}, R_{3}, \ldots$$

A single thinking circle is: `state -> action -> reward -> state'`

# Dyanmics of MDPs

- state $s$: we already observe
- action $a$: determinated by ourselves
- *reward $r$: we cannot control
- *new state $s'$: we cannot control

So the rest of 2 brings dynamics, conditional on 1st 2 things:

$$p\left(s^{\prime}, r \mid s, a\right) \doteq \operatorname{Pr}\left\{S_{t}=s^{\prime}, R_{t}=r \mid S_{t-1}=s, A_{t-1}=a\right\}$$

we did not know exactly distribution $p$ has, but we know $p$ defines the dynamics of the MDP. While $s'$ and $r$ are 2 random variables. $p$ is the joint density function of these two.

## Properties of $p$

first, no doubt that:

$$\sum_{s^{\prime} \in S} \sum_{r \in \mathcal{R}} p\left(s^{\prime}, r \mid s, a\right)=1$$


of course you can get marginal density of $s'$ by summing over $t$:

$$p\left(s^{\prime} \mid s, a\right) \doteq \operatorname{Pr}\left\{S_{t}=s^{\prime} \mid S_{t-1}=s, A_{t-1}=a\right\}=\sum_{r \in \mathcal{R}} p\left(s^{\prime}, r \mid s, a\right)$$

or marginal density of $r$, and get expected $r$ further:

$$r(s, a) \doteq \mathbb{E}\left[R_{t} \mid S_{t-1}=s, A_{t-1}=a\right]=\sum_{r \in \mathcal{R}} r \sum_{s^{\prime} \in S} p\left(s^{\prime}, r \mid s, a\right)$$

keep in mind that the expected $r$ is not a value, is a function of tuple (s,a).

we could skip sum over $s'$ at first, to get a function of over a 3-elements tuple (s, a, s'):

$$r\left(s, a, s^{\prime}\right) \doteq \mathbb{E}\left[R_{t} \mid S_{t-1}=s, A_{t-1}=a, S_{t}=s^{\prime}\right]=\sum_{r \in \mathcal{R}} r \frac{p\left(s^{\prime}, r \mid s, a\right)}{p\left(s^{\prime} \mid s, a\right)}=\sum_{r \in \mathcal{R}} r \times p(r|s', s, a)$$


# Reward

the objective is to maximum the total reward $G$


$$G_{t} \doteq R_{t+1}+\gamma R_{t+2}+\gamma^{2} R_{t+3}+\cdots=\sum_{k=0}^{\infty} \gamma^{k} R_{t+k+1}$$

{{% callout warning %}}
An important recurrsive equation is :

$$G_{t}=R_{t+1}+\gamma G_{t+1}$$

{{% /callout %}}


# Policies and Value Func

- Policy: Given states, the distribution of actions: $\pi(a \mid s)$
- Value function: estimate "how good it is".

## Value function of state (state value func)

$v_{\pi}(s)$: value function of a state $s$ under a policy $\pi$
- the expected return when starting in s and following $\pi$ thereafter

$$v_{\pi}(s) \doteq \mathbb{E}_{\pi}\left[G_{t} \mid S_{t}=s\right]=\mathbb{E}_{\pi}\left[\sum_{k=0}^{\infty} \gamma^{k} R_{t+k+1} \mid S_{t}=s\right]$$

this is function of s. since policy is a distribution $\pi(a \mid s)$, and here when s is given, the expection over policy is actually over $a$.

## Value function of action (action value func)

the value of taking action $a$ in state $s$ under a policy $\pi$

$$q_{\pi}(s, a) \doteq \mathbb{E}_{\pi}\left[G_{t} \mid S_{t}=s, A_{t}=a\right]=\mathbb{E}_{\pi}\left[\sum_{k=0}^{\infty} \gamma^{k} R_{t+k+1} \mid S_{t}=s, A_{t}=a\right]$$



{{% callout warning %}}
An important recurrsive equation is :

<img src="https://cdn.mathpix.com/snip/images/HLBDVQqvaiun4uvTTV6js4_WqifX0Apg0E-HQWrbQRU.original.fullsize.png" />

{{% /callout %}}


## Bullman Equation

Lets review the above equation:

First, we have:

$$
\begin{aligned}
V_{n(s)} &=E_{i t}\left(G_{t} \mid S_{t=s}\right) \\
&=E_{i}\left(R_{t+1}+\gamma G_{t+1} \mid S_{t}=s\right)
\end{aligned}
$$


### 1. What is $E_{\pi}$?

consider a full cycle: $(s, a, r, s')$,  we want to get the expected value of $r$:
- s is given
- a is a RV; that determinated by $\pi(a|s)$
- r, s' are RVs


<img src="https://cdn.mathpix.com/snip/images/STyb-z2Y59twppIxman8QLnuM_Is3sghXNlD_akn3-M.original.fullsize.png" />

### 2. what is the rest: $E_{\pi}\left(\gamma G_{t+1} \mid s_{1}=s\right)$ ?

Notice that $G_{\text {tri }}$ is only related to $s^{\prime}$:


So the rest:

$$
\begin{aligned}
&=\gamma \sum_{s^{\prime}} p\left(s^{\prime} | s, a\right) \\
&=\gamma \sum_{a} \pi(a | s) \sum_{s^{\prime}} p\left(s^{\prime} | s, a\right)\left(G_{t+1} | s_{t+1} = s^{\prime}\right) \\
&=\gamma \sum_{a} \pi(a | s) \sum_{s} p\left(s^{\prime} | s, a \right) V_{\pi}\left(s^{\prime}\right)
\end{aligned}
$$


### 3. overall


$$
\text { Due to}  \quad \sum_{s^{\prime}} p\left(s^{\prime} \mid s, a\right)=\sum_{s^{\prime}} \sum_{r} p\left(s^{\prime}, r | s, a\right)
$$

so we can derive $V_{\pi}(s)$ as :

$$\begin{aligned} V_{\pi(s)} &=E_{\pi}\left(R_{t+1}+\gamma G_{t+1} \mid s_{t=s}\right) \\ &=\sum_{n} \pi(a \mid s) \sum_{s^{\prime}} \sum_{r} p\left(s^{\prime}, r | s, a\right)\left[r+\gamma V_{\pi}\left(s^{\prime}\right)\right] \end{aligned}$$

known as Bellman Equation (under MDP and Policy $\pi$)

### 4. meaning
- Bellman Equation shows
a relationship between the `value of a state` and the `values of
its successor states`
- It states that the `value of the start state` must equal `the
(discounted) value of the expected next state`, plus `the reward expected along the way`.


---
An graphic example of Bellman equation


<img src="https://cdn.mathpix.com/snip/images/ECmHtGPDa5d_LjyrZG1Et8xZqrstIvCqp-gtRBPGYXU.original.fullsize.png" />

- branch = 3: 3 possible action due to policy
- solid circle: a state-action pair (s, a)
- then, each action has 2 possible (r, s') pair

---