# Learning the Nuts and Bolts of XGBoost


## Table of Contents

- [Introduction](#introduction)
- [Fundamentals of Tree Boosting](#fundamentals-of-tree-boosting)
- [Gradient Tree Boosting](#gradient-tree-boosting)
- [Expanding the Regularized Objective](#expanding-the-regularized-objective)
- [Optimal Leaf Weights](#optimal-leaf-weights)
- [References](#references)

## Introduction

Gradient Tree Boosting has gained wide popularity in recent years and has been used extensively in machine learning competitions. This guide provides a deep dive into the **mathematics behind XGBoost** and the design principles that make it scalable.

### Why XGBoost?

Among the 29 challenge-winning solutions published at Kaggle's blog during 2015, **17 solutions used XGBoost**. This remarkable adoption rate reflects the algorithm's effectiveness and practicality in solving real-world problems.

### Contents Summarized

This blog explores:

1. **Tree Boosting Fundamentals** - Understanding the regularized objective function
2. **Gradient Tree Boosting** - The core algorithm using second-order Taylor approximations
3. **Optimal Leaf Weights** - Deriving the formula for computing leaf predictions
4. **System Design** - Split-finding methods and scalability techniques

### Reference Paper

For more details, refer to the original paper: [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/pdf/1603.02754) by Chen & Guestrin (2016).

---

## Fundamentals of Tree Boosting

### Dataset Notation

Let $D$ represent our training dataset:

$$
D = \{(x_i, y_i)\} \quad \text{where} \quad |D| = n, \quad x_i \in \mathbb{R}^m, \quad y_i \in \mathbb{R}
$$

- **n**: number of training instances
- **m**: number of features
- **$x_i$**: feature vector for instance i
- **$y_i$**: target value for instance i

### Ensemble Model Prediction

XGBoost combines K regression trees to make predictions:

$$
\hat{y_i} = \phi(x_i) = \sum_{k=1}^K f_k(x_i), \quad f_k \in \mathcal{F}
$$

where $\mathcal{F}$ is the space of regression trees.

### Regularized Objective Function

The goal is to minimize the following regularized objective:

$$
L(\phi) = \sum_{i=1}^n l(\hat{y_i}, y_i) + \sum_{k=1}^K \Omega(f_k)
$$

where:

- **$l(\hat{y_i}, y_i)$** - A differentiable loss function (e.g., squared loss, logistic loss), we prefer convex function here.
- **$\Omega(f_k)$** - A regularization term that penalizes model complexity

### Regularization Term

The regularization term for each tree is defined as:

$$
\Omega(f) = \gamma T + \frac{1}{2} \lambda \| w \|^2
$$

**Components:**

- **$\gamma T$** - Penalty for the number of leaves in the tree (T = number of leaves), we generally control depth of tree (if depth of tree is d, then number of leaves is bounded by O($2^d$))
- **$\frac{1}{2} \lambda \| w \|^2$** - L2 regularization on leaf weights
- **$\gamma$** - Hyperparameter for minimum split gain
- **$\lambda$** - Hyperparameter for L2 regularization
- **$w_j$** - Predicted score on the j-th leaf

### Key Insight

Unlike traditional gradient boosting, XGBoost regularizes:
- The number of leaves per tree
- The magnitude of leaf weights
- If we remove the regularization term, this convert to loss function of a traditional gradient boosting

---

## Gradient Tree Boosting

### Sequential Tree Fitting

In boosting, we fit trees sequentially. Let $f_t$ be the t-th tree added to the ensemble. The objective function at iteration t becomes:

$$
L(t) = \sum_{i=1}^{n} l(y_i, \hat{y}_i^{(t-1)} + f_t(x_i)) + \Omega(f_t)
$$

where:

- **$\hat{y}_i^{(t-1)}$** - Current prediction before adding the t-th tree
- **$f_t(x_i)$** - Predicted value from the new tree (or incremental change in prediction value, often compared with h in our taylor's series)

### Taylor Series Approximation

The paper usage  a **second-order Taylor expansion** of the loss around $\hat{y}_i^{(t-1)}$
we will come to know why 2nd order approximation is a better choice during derivation, what can happen if we use higher degree approximation?

**General Taylor Expansion:**

For a two variable function $f(x, y + h)$, the second-order Taylor approximation is (change happening in only one variable):

$$
f(x, y + h) \approx f(x, y) + \frac{\partial f(x, y)}{\partial y}h + \frac{1}{2} \frac{\partial^2 f(x, y)}{\partial y^2}h^2
$$

**Applying to Loss Function:**

Setting:
- $y = \hat{y}_i^{(t-1)}$ (current prediction)
- $h = f_t(x_i)$ (increment from new tree)
- $x = y_i$ (which is constant) (no change happening here, h is added only to $\hat{y}_i^{(t-1)}$)

We expand the loss around the current prediction:

$$
l(y_i, \hat{y}_i^{(t-1)} + f_t(x_i)) \approx l(y_i, \hat{y}_i^{(t-1)}) + g_i f_t(x_i) + \frac{1}{2} h_i f_t(x_i)^2
$$

### First and Second-Order Derivatives

Define the first-order and second-order derivatives of the loss:

**First-order gradient:**

$$
g_i = \frac{\partial l(y_i, \hat{y}_i^{(t-1)})}{\partial \hat{y}_i^{(t-1)}}
$$

**Second-order derivative (Hessian):**

$$
h_i = \frac{\partial^2 l(y_i, \hat{y}_i^{(t-1)})}{\partial (\hat{y}_i^{(t-1)})^2}
$$

### Simplified Objective

Removing the constant term $l(y_i, \hat{y}_i^{(t-1)})$ (which doesn't affect optimization), the objective becomes:

$$
L(t) = \sum_{i=1}^{n} \left[ g_i f_t(x_i) + \frac{1}{2} h_i f_t(x_i)^2 \right] + \Omega(f_t)
$$

This is the **simplified objective** that XGBoost optimizes at each iteration.

---

## Expanding the Regularized Objective

### Substituting the Regularization Term

Substituting $\Omega(f_t)$ into the objective:

$$
\tilde{L}^{(t)} = \sum_{i=1}^{n} \left[ g_i f_t(x_i) + \frac{1}{2} h_i f_t(x_i)^2 \right] + \gamma T + \frac{1}{2} \lambda \sum_{j=1}^{T} w_j^2
$$

### Grouping by Leaves

Since each training instance belongs to exactly one leaf, we can reorganize terms by leaf node. Also, the prediction in a leaf node is constant value. Let $I_j$ be indicator variable indicating the set of instances in leaf j:

$$
\tilde{L}^{(t)} = \sum_{j=1}^{T} \left[ \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 \right] + \gamma T
$$

This can also be written as:

$$
\tilde{L}^{(t)} = \sum_{j=1}^{T} \left[ \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 + \gamma \right]
$$

### Key Observation

Notice that the objective function separates into **independent terms for each leaf**. This is crucial because it means we can optimize each leaf weight independently!

---

## Optimal Leaf Weights

### Decomposition into Leaf-Wise Losses

Since each leaf's objective is independent:

$$
L(t) = \sum_{j=1}^{T} L(t_j)
$$

where:

$$
L(t_j) = \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 + \gamma
$$

### Optimization

To find the optimal weight $w_j^*$, we take the derivative with respect to $w_j$ and set it to zero:

$$
\frac{\partial L(t_j)}{\partial w_j} = \sum_{i \in I_j} g_i + \left( \sum_{i \in I_j} h_i + \lambda \right) w_j = 0
$$

Solving for $w_j$:

$$
w_j^* = -\frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda}
$$

### Interpretation

- **Numerator** ($\sum_{i \in I_j} g_i$) - Sum of gradients for instances in leaf j
- **Denominator** ($\sum_{i \in I_j} h_i + \lambda$) - Sum of Hessians plus regularization
- The ratio balances the **direction of improvement** (gradient) with **curvature** (Hessian)
- Higher $\lambda$ leads to smaller leaf weights, preventing overfitting

### Optimal Objective Value

Substituting $w_j^*$ back into $L(t_j)$:

$$
L(t_j) = \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 + \gamma
$$

$$
L(t_j) = \left( - \sum_{i \in I_j} g_i \right)\frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda} + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) {(-\frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda})}^2 + \gamma
$$


$$
L(t_j)^* = -\frac{1}{2} \frac{\left(\sum_{i \in I_j} g_i\right)^2}{\sum_{i \in I_j} h_i + \lambda} + \gamma
$$

This represents the **minimum loss value for a leaf** for a given tree structure. Since each data points lie in a single leaf. We can find the optimal value 

---

## Split Criterion
At each node, we try different splits value that try to minimize the splitting criterion. Split criterion for a node is defined

$$
-\frac{1}{2} \frac{\left(\sum_{i \in I_(node)} g_i\right)^2}{\sum_{i \in I_(node)} h_i + \lambda} + \gamma - \left[ -\frac{1}{2} \frac{\left(\sum_{i \in I_(left)} g_i\right)^2}{\sum_{i \in I_(left)} h_i + \lambda} + \gamma  -\frac{1}{2} \frac{\left(\sum_{i \in I_(right)} g_i\right)^2}{\sum_{i \in I_(right)} h_i + \lambda} + \gamma \right]
$$

$$
\frac{1}{2}\left[ \frac{\left(\sum_{i \in I_(right)} g_i\right)^2}{\sum_{i \in I_(right)} h_i + \lambda}  +  \frac{\left(\sum_{i \in I_(left)} g_i\right)^2}{\sum_{i \in I_(left)} h_i + \lambda} - \frac{\left(\sum_{i \in I_(node)} g_i\right)^2}{\sum_{i \in I_(node)} h_i + \lambda} \right]- \gamma
$$

where $n_(node) = n_(left) + n_(right)$



## Key Takeaways

1. **Taylor Approximation Enables Optimization** - Second-order Taylor expansion converts the problem into a tractable quadratic form. Optimal leaf weights have a simple closed-form solution depending only on gradients, Hessians, and regularization. Note if we have taken higher order approximation of Taylor's series, we might need to solve quadratic equation for getting the optimal value

2. **Leaf Independence** - The objective separates into independent leaf-wise losses, enabling efficient optimization

3. **Regularization in Action** - The $\lambda$ term in the denominator naturally prevents extreme leaf weights, controlling model complexity. Note $\gamma$ doesn't play role in the optimal value, but it's play important role in optimal loss value, thus split criterion

4. **Generality** - These formulas work for any differentiable loss function, making XGBoost applicable to regression, classification, and ranking tasks, we prefer convex loss function.

---

## Next Steps

The mathematical foundation above enables XGBoost's key algorithms:

- **Split Finding** - Computing gain for potential splits to grow trees greedily
- **Pruning** - Using the objective value to determine when to stop splitting
- **System Optimization** - Histogram-based splitting, parallel training, and out-of-core processing

---

## References

- Chen, T., & Guestrin, C. (2016). [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/pdf/1603.02754). In *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining* (pp. 785-794).

---

## Contributing

Found an error or have suggestions? Feel free to open an issue or submit a pull request!

## License

This guide is provided as-is for educational purposes.
