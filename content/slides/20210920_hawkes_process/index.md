---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "20210926_modeling"
summary: ""
authors: 
- Dylan Yang
tags: []
categories: []
date: 2021-09-20T10:41:52-04:00
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  #   Available highlight themes listed in: https://highlightjs.org/static/demo/
  #   Use lower case names and replace space with hyphen '-'
  highlight_style: github

  diagram: true
  diagram_options:
    # Mermaid diagram themes include: default,base,dark,neutral,forest
    theme: base

  # RevealJS slide options.
  # Options are named using the snake case equivalent of those in the RevealJS docs.
  reveal_options:
    controls: true
    progress: true
    slide_number: c/t  # true | false | h.v | h/v | c | c/t
    center: true
    rtl: false
    mouse_wheel: true
    transition: fade  # none/fade/slide/convex/concave/zoom
    transitionSpeed: default  # default/fast/slow
    background_transition: slide  # none/fade/slide/convex/concave/zoom
    touch: true
    loop: false
    menu_enabled: true
---

# Marked Multi TPP

**2021 09 26 Modeling Cache**

Table of Content:

- [Marked Multi TPP](#marked-multi-tpp)
- [Problem Setting](#problem-setting)
- [Modeling](#modeling)
  - [$\lambda(A_i, t_n|\mathbb{H}_{t_n})$](#lambdaa_i-t_nmathbbh_t_n)
  - [$h_{A_i}^{t_n}$ details:](#h_a_it_n-details)
  - [$f_{mark}(A_i, T_k, t_n \mid \mathbb{H}_{t_n})$:](#f_marka_i-t_k-t_n-mid-mathbbh_t_n)

---

# Problem Setting

Observation Sequence $S$: $S = \left\{  (A_i, T_k, t_n)     \right\}_{n=1}^{N}$
- $A_i$: the $i$th acquirer who envolve in the $n$th MA event happend at time $t$
- $T_k$: the $k$th target who envolve in the $n$th MA event happend at time $t$
- $t$: time point when the $n$th MA event happened

---

# Modeling

From a broad view:

$$\lambda(A_i, T_k, t_n \mid \mathbb{H}_{t_n}) = \lambda(A_i, t_n|\mathbb{H}_{t_n})\times f_{mark}(A_i, T_k, t_n \mid \mathbb{H}_{t_n})$$
- $\sum_{i, k} f_{mark}(\cdot, t_n)=1$
- $f_{mark}(A_i, T_k, t_n \mid \mathbb{H}_{t_n}) = f_{mark}(A_i, T_k, t_n)$

Notations:
- $\mathbb{H}_{t_n}$: the history events before $t_n$

---

## $\lambda(A_i, t_n|\mathbb{H}_{t_n})$

$$\lambda(A_i, t_n|\mathbb{H}_{t_n}) = f_{\lambda}\left(h_{A_i}^{t_n} + b_{A_i}^{t_n} + w_T(t- t_n)\right)$$

$$NEED \;FIN \;PAPER \;READ: \;to \;justify \;model \;design$$

<br><br>

$h_{A_i}^{t_n}$, the historical information, mimic the 2nd summation term of Hawkes Process.

$$h_{A_i}^{t_n} = w_P^T P_{A_i}^{t_n^-} + w_I^T I_{A_i}^{t_n^-}$$
+ $t_n^-$ is the time when the most recent MA event happened right before $t_n$

<br><br>

$b_{A_i}^{t_n}$: the base CIF rate, depends on the financial condition of the acquiror:

$$b_{A_i}^{t_n} = f_{base}(w_S^T S_{A_i}^{t_n^-})$$
- $S_{A_i}^{t_n^-}$: the vector that construct by financial condition variables.

<br><br>

$w_T(t- t_n)$: time shifting, allow CIF change constantly

---
## $h_{A_i}^{t_n}$ details:

<br><br>


$w_P^T P_{A_j}^{t_n^-}$: modeling "peer pressure", obtain by a undirected weighted graph based on TNIC. If we focus on $j$th acquiror who did not involved in the event happened at time $t_n$( $i$ was cumulating influence on $j$ at time $t_n$)

$$MSG_{A_e\rightarrow A_j}^{t_n^-} = A_{e, i}^{t_n^-} f_{MSG}(z_{A_j}^{t_n^{--}},\; z_{A_e}^{t_n^{--}})$$
$$P_{A_j}^{t_n^-} = GRU_{MSG}(MSG_{A_e\rightarrow A_j}^{t_n^-},\; P_{A_j}^{t_n^{--}})$$
- $z$: node(acquiror) embedding
  - init by fin feature vectors
- $A$: Adjacency matrix from TNIC. A undirected weighted graph.
- $P$: "peer influence" vector

$w_P^T P_{A_i}^{t_n^-}$: for $A_i$, the participating acquiror of the MA event happened in $t_n$, use GRU to update it's $P$:

$$P_{A_i}^{t_n^-} = GRU_{self}(, P_{A_j}^{t_n^{--}})$$


<br><br>

$I_{A_i}^{t_n^-}$: modeling the "industry trend", obtain by I/O network(A directed weighted graph).

$$NEED \;FIN \;PAPER \;READ: \;to \;decide \;model \;structure$$

---

## $f_{mark}(A_i, T_k, t_n \mid \mathbb{H}_{t_n})$:

Modeling the Choice: compute compatability between $A_i$ and $T_k$

$$f_{mark}(A_i, T_k, t_n \mid \mathbb{H}_{t_n})= f_{mark}(w_1^T[S_{A_i}^{1, t_n^-}; S_{T_k}^{1, t_n^-}],\; w_2^T[S_{A_i}^{2, t_n^-}; S_{T_k}^{2, t_n^-}], ......)$$
+ $S$: synergy variables


$$NEED \;FIN \;PAPER \;READ: \;to \;decide \;variable \;choose$$

---

The Overall Likelihood:

$$logL = \sum_{n=1}^{N} log \lambda_{t_n} - \sum_{k=1}^{T_N} \sum_{i=1}^{A_N}\int_0^{T} \lambda_{A_i, T_k}(t) d t$$
- the 1st term is sum over all CIF of "positive samples"
- $T_N$, $A_N$ are the total number of target, acquirer respectively.




---
