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

A Gaussian Process provides a way to compute this intimidating integral analytically. It does this by placing a GP prior directly on the *function* `f(x)` instead of on parameters `w`, moving us from a parameter-space view to a function-space view.

### Mathematical Formulation of a GP

A Gaussian Process is fully specified by its **mean function** `m(x)` and its **covariance function** (or **kernel**) `k(x, x')`.

$$
f(x) \sim \mathcal{GP}(m(x), k(x, x'))
$$

*   **Mean Function `m(x)`:** This represents the expected value of the function at input `x`. It is often set to zero for simplicity.
    $$
    m(x) = \mathbb{E}[f(x)]
    $$
*   **Covariance Function `k(x, x')`:** This is the heart of the GP. It models the covariance between the function's values at two different points, `x` and `x'`. It encodes our assumptions about the function's smoothness and behavior.
    $$
    k(x, x') = \text{Cov}(f(x), f(x'))
    $$
    A common choice is the a Squared Exponential (or RBF) kernel:
    $$
    k(x, x') = \sigma_f^2 \exp\left(-\frac{1}{2l^2}(x-x')^2\right)
    $$

Given a set of training points `X` and test points `X_*`, the GP assumes that the function values `f` and `f_*` are jointly Gaussian:

$$
\begin{bmatrix} \mathbf{f} \\ \mathbf{f}_* \end{bmatrix} \sim \mathcal{N} \left( \mathbf{0}, \begin{bmatrix} K(X, X) & K(X, X_*) \\ K(X_*, X) & K(X_*, X_*) \end{bmatrix} \right)
$$

Using the property of conditional distributions of Gaussians, we can derive the posterior predictive distribution `P(f_* | X_*, X, f)`, which will also be a Gaussian with an analytical mean and variance. This is the mechanism that allows Bayesian Optimization to work.