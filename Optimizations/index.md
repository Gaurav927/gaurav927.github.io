# Introduction

The core problem in Machine learning is parameter estimation, this require solving an optimization problem, where we try to find the values for a set of variables $\theta \in \mathbb{R}^{D}$, where D is number of parameter

$$
\theta^{*} = \arg\min_{\theta \in \Theta} \mathcal{L}(\theta)
$$

For the rest of blog, we are focussing on continuous optimization

## Local vs Global Optimization

Formally, we say

A point $\theta^{*}\in\Theta$ is a local minimizer of $\mathcal{L}$ if there exists $\varepsilon>0$ such that
$$
\forall \theta\in\Theta,\ \|\theta-\theta^{*}\|<\varepsilon \implies \mathcal{L}(\theta)\ge \mathcal{L}(\theta^{*}).
$$

### Optimality Conditions for local vs global optima
For continuous, twice differentiable function, we can find the point which correspond to local minima

Let g($\theta$) = $\nabla \mathcal{L} (\theta)$ and H($\theta$) = $\nabla^{2}\mathcal{L} (\theta)$

* **Necessary Condition**: If $\theta^*$ is a local minimum, then we must have $g^* = 0$ and $H^*$ must be positive semi-definite (the point might be a **saddle point**).

* **Sufficient Condition**: If $g^* = 0$ and $H^*$ is positive definite, then $\theta^*$ is a local minimum.

### Constrained vs Unconstrained Optimization
Constrained vs Unconstrained Optimization (mathematical summary)

- Unconstrained problem:
    Minimize L(θ) over θ ∈ Θ, typically Θ = R^D (or an open subset).
    Optimality (twice differentiable):
    - First‑order: ∇L(θ*) = 0 (stationarity).
    - Second‑order: Hessian H(θ*) = ∇²L(θ*) PSD for a local minimizer, PD for a strict local minimizer.

- Constrained problem:
    Minimize L(θ) subject to equality and inequality constraints

    $$\theta \in C = \{ \theta : c_i(\theta) = 0,\ i=1,\ldots,m;\ d_j(\theta) \le 0,\ j=1,\ldots,p \}.$$

### Convex vs nonconvex Optimization

**Convex Set**

A set is convex if for every \(x,y \in C\) and every \(t \in [0,1]\) the convex combination lies in \(C\):
$$
\forall x,y\in C,\ \forall t\in[0,1]\quad (1-t)x + t y \in C.

$$
Equivalently, \(C\) contains the entire line segment between any two of its points. 
The convex hull of a set $S$ is the smallest convex set containing $S$, i.e., all finite convex combinations
$$
\sum_i \alpha_i x_i,\quad \alpha_i\ge0,\ \sum_i\alpha_i=1.
$$

**Theorem (Second‑order characterization of convexity).**

Let $f: C\subset\mathbb{R}^D \to \mathbb{R}$ be twice continuously differentiable on a convex open set $C$. Then:

1. $f$ is convex on $C$ if and only if its Hessian is positive semidefinite everywhere:
$$
\forall x\in C,\quad H_f(x)=\nabla^2 f(x)\succeq 0,
$$
i.e.
$$
v^\top H_f(x)\,v\ge 0\quad\forall v\in\mathbb{R}^D.
$$

2. If $\nabla^2 f(x)\succ 0$ for all $x\in C$ (Hessian positive definite) then $f$ is strictly convex on $C$. The converse need not hold (e.g. $f(x)=x^4$ on $\mathbb{R}$ is strictly convex but $f''(0)=0$).

### Convex Function

A function f is convex if its epigraph (the set of points above the graph) is convex set.

**Jensen's condition for convexity**

A function $f: C\subset\mathbb{R}^D \to \mathbb{R}$ on a convex set $C$ is convex if and only if it satisfies Jensen's inequality in any of the equivalent forms below.

- Discrete (finite convex combinations) form:  
    For any points $x_1,\dots,x_n\in C$ and weights $\alpha_i\ge 0$ with $\sum_{i=1}^n\alpha_i=1$,
    $$
    f\!\left(\sum_{i=1}^n \alpha_i x_i\right) \le \sum_{i=1}^n \alpha_i f(x_i).
    $$

- Two‑point (standard) form:  
    For any $x,y\in C$ and $t\in[0,1]$,
    $$
    f((1-t)x + t y) \le (1-t)f(x) + t f(y).
    $$

- Expectation form (random variable $X$ taking values in $C$):  
    $$
    f(\mathbb{E}[X]) \le \mathbb{E}[f(X)].
    $$

If the inequalities are strict whenever the points (or distribution) are not degenerate, then $f$ is strictly convex.


## Reference

Kevin Murphy, Probabilistic Machine Learning, Chapter 8.