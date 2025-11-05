---
layout: default
title: Bayesian Optimization
permalink: /bayesian-optimization/
---

# Bayesian Optimization

## Why Bayesian Optimization?

With numerous optimization algorithms available, what makes Bayesian Optimization a valuable tool? It particularly excels in optimizing **black-box functions** that are expensive to evaluate. For example, tuning hyperparameters of a deep learning model, optimizing a chemical process, or finding the best design in an engineering simulation.

To understand Bayesian Optimization, we must first explore its core component: the **Gaussian Process**.

## The Gaussian Process (GP)

A Gaussian Process is a powerful tool for modeling unknown functions. It defines a distribution over functions, and its properties are derived from the multivariate normal distribution.

Let's review some key properties of normal distributions that are fundamental to GPs. If `X` and `Y` are random variables from a multivariate normal distribution, then:

1.  **Linear Combinations are Normal:** Any linear combination, such as `aX + bY`, results in a normal distribution.
2.  **Marginal Distributions are Normal:** The individual distribution of `X` (and `Y`), obtained by integrating out the other variables, is normal.
3.  **Conditional Distributions are Normal:** The distribution of `X` given a specific value of `Y` (i.e., `P(X|Y=y)`) is also normal.

These closure properties make the math tractable and are central to how GPs work.

### From Parameter Estimation to Function-Space View

In many machine learning models, we estimate parameters from data. Two common methods are:

*   **Maximum Likelihood Estimation (MLE):** Finds parameters `w` that maximize the likelihood of the observed data, `P(D|w)`.
*   **Maximum a Posteriori (MAP):** Extends MLE by incorporating a prior belief about the parameters, `P(w)`. It finds `w` that maximizes the posterior probability, `P(w|D)`.

$$
\underbrace{\arg\max_{w} P(w \mid D)}_{\text{MAP}} = \arg\max_{w} \underbrace{P(D \mid w)}_{\text{Likelihood}} \underbrace{P(w)}_{\text{Prior}}
$$

Both MLE and MAP provide a **point estimate** for the parameters. Bayesian inference takes this a step further. Instead of finding the *single best* set of parameters, it computes the full **posterior distribution** `P(w|D)`.

When we make a prediction for a new point `x`, we don't use a single `w`. Instead, we average the predictions of all possible parameters, weighted by their posterior probability. This is called marginalization.

Let `D` be our training data `D = {(x_i, y_i)}`. The predictive distribution for a new output `y` at input `x` is:

$$
\begin{align*}
P(y | x, D) &= \int P(y, w | x, D) \,dw && \text{(Marginalize out } w) \\
&= \int P(y | x, w) P(w | D) \,dw && \text{(Chain Rule)}
\end{align*}
$$

This integral averages the predictions `P(y | x, w)` from every possible model `w`, weighted by the posterior `P(w | D)`.

### Kernel Function
This is second building block for our final masterpiece Gaussian Process. A Kernel is seen in Support Vector Machine(which help us projection in infinite dimesion in case of RBF). It's the same `kernel` we use in Gaussian Process. Lets look at few properties of Kernel function.
Suppose `K(X, Y)` is a kernel function, then following properties must hold true. This help us in desiging any kernel function used in any area.

1. K(x, y) = f(x) f(y)
2. K is also symmetric from property  K(y, x) = f(y)f(x)
3. Kernel matrix must be positive semidefnite matrix ($\lambda$ >= 0), where $\lambda$ is eigen value of Kernel matrix.

This mainly form the basics of Guassian Process.
The Covariance matrix in GP is given by Gram matrix.
Given a set of vectors, $S = \{x_1, \dots, x_n\}$, the Gram matrix is defined as the 
$n \times n$ matrix $G$ whose entries are given by:
$$
G_{ij} = \langle x_i, x_j \rangle
$$
If we are using a kernel function $\kappa$ to evaluate the inner products in a feature 
space with a feature map $\phi$, the associated Gram matrix has entries:
$$
G_{ij} = \langle \phi(x_i), \phi(x_j) \rangle = \kappa(x_i, x_j)
$$
### Joint Distribution
$$
{Y} =
\begin{bmatrix}
y_{1}  \\
y_{2} \\
\end{bmatrix} 
$$

$$
Y \thicksim N(\mu, \Sigma)
$$
$$
\boldsymbol{\mu} = 
\begin{bmatrix}
\mu_{1}  \\
\mu_{2} \\
\end{bmatrix} 
\\
$$
$$
\boldsymbol{\Sigma} = 
\begin{bmatrix}
\Sigma_{11} & \Sigma_{12} \\
\Sigma_{21} & \Sigma_{22} \\
\end{bmatrix}
$$

### Conditional Distribution
$$
p(y_{1} | x_{1}, x_{2}, y_{2}) \thicksim N(\mu_{1|2}, \Sigma_{2|1})
$$

$$
\mu_{1|2} \quad=\quad \mu_{2} \quad + \quad \Sigma_{21}\Sigma^{-1}_{11}(y_{1} - \mu_{1})
$$

$$
\Sigma_{2|1} = \Sigma_{22} - \Sigma_{21}\Sigma^{-1}_{11}\Sigma_{12}
$$

where $y_{1}$ can be a vector or scalar


## Refernce 
https://people.eecs.berkeley.edu/~jordan/kernels/0521813972c03_p47-84.pdf








