# Learning the Nuts and Bolts of XGBoost

In this blog, we aim to understand XGBoost from a Mathematical perspective, delving deep into its derivations.


## Table of Contents

- [Introduction](#introduction)
- [Fundamentals of Tree Boosting](#fundamentals-of-tree-boosting)
- [Gradient Tree Boosting](#gradient-tree-boosting)
- [Expanding the Regularized Objective](#expanding-the-regularized-objective)
- [Optimal Leaf Weights](#optimal-leaf-weights)
- [Split Criterion](#split-criterion)
- [References](#references)

## Introduction

Gradient Tree Boosting has gained wide popularity in recent years and has been used extensively in machine learning competitions. This guide provides a comprehensive exploration of the **mathematics behind XGBoost** and the optimizations that enable its scalability.

### Why XGBoost?

One of the most widely used winning algorithms in Kaggle Competitions, it is frequently used by machine learning scientists and data scientists.

### Contents Summarized

This blog explores:

1. **Tree Boosting Fundamentals** - Understanding the regularized loss function
2. **Gradient Tree Boosting** - The core algorithm using second-order Taylor approximations
3. **Optimal Leaf Weights** - Deriving the formula for computing leaf predictions
4. **System Design** - Split-finding methods and scalability techniques

### Reference Paper

Refer to original paper: [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/pdf/1603.02754) by Chen & Guestrin (2016).

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

XGBoost combines K models to make predictions. It can be a classification tree or a regression tree:

$$
\hat{y_i} = \phi(x_i) = \sum_{k=1}^K f_k(x_i), \quad f_k \in \mathcal{F}
$$

where $\mathcal{F}$ is the space of regression trees.

### Regularized Objective Function

The goal is to minimize the following regularized objective:

$$
L(\phi) = \sum_{i=1}^n l(\hat{y_i}, y_i) + \sum_{k=1}^K \Omega(f_k)
$$

Where:

- **$l(\hat{y_i}, y_i)$** - A differentiable loss function (e.g., squared loss, logistic loss); we prefer a convex function here.
- **$\Omega(f_k)$** - A regularization term that penalizes model complexity

### Regularization Term

The regularization term for each tree is defined as:

$$
\Omega(f) = \gamma T + \frac{1}{2} \lambda \| w \|^2
$$

**Components:**

- **$\gamma T$** - Penalty for the number of leaves in the tree (T = number of leaves), we generally control the depth of the tree (if the depth of the tree is d, then the number of leaves is bounded by O($2^d$))
- **$\frac{1}{2} \lambda \| w \|^2$** - L2 regularization on leaf predictions.
- **$\gamma$** - Hyperparameter for minimum split gain
- **$\lambda$** - Hyperparameter for L2 regularization
- **$w_j$** - Predicted score on the j-th leaf

### Key Insight

Unlike traditional gradient boosting, XGBoost regularizes:
- The number of leaves per tree
- The magnitude of leaf weights
- If we remove the regularization term, this converts to the loss function of a traditional gradient boosting

---

## Gradient Tree Boosting

### Sequential Tree Fitting

In boosting, we fit trees sequentially. Let $f_t$ be the t-th tree added to the ensemble. The objective function at iteration t becomes:

$$
L(t) = \sum_{i=1}^{n} l(y_i, \hat{y}_i^{(t-1)} + f_t(x_i)) + \Omega(f_t)
$$

Where:

- **$\hat{y}_i^{(t-1)}$** - Current prediction before adding the t-th tree
- **$f_t(x_i)$** - Predicted value from the new tree (or incremental change in prediction value, often compared with h in our Taylor series)

### Taylor Series Approximation

The paper usage  a **second-order Taylor expansion** of the loss around $\hat{y}_i^{(t-1)}$
We will come to understand why the 2nd-order approximation is a better choice during the derivation and what can happen if we use a higher-degree approximation..

**General Taylor Expansion:**

For a two-variable function $f(x, y + h)$, the second-order Taylor approximation is (change happening in only one variable):

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

The above expression is the **simplified objective** that XGBoost optimizes at each iteration.

---

## Expanding the Regularized Objective

### Substituting the Regularization Term

Substituting $\Omega(f_t)$ into the objective:

$$
\tilde{L}^{(t)} = \sum_{i=1}^{n} \left[ g_i f_t(x_i) + \frac{1}{2} h_i f_t(x_i)^2 \right] + \gamma T + \frac{1}{2} \lambda \sum_{j=1}^{T} w_j^2
$$

### Grouping by Leaves

Since each training instance belongs to exactly one leaf, we can reorganize terms by leaf node. Also, the prediction in a leaf node is a constant value. Let $I_j$ be an indicator variable indicating the set of instances in leaf j:

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

Where:

$$
L(t_j) = \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 + \gamma
$$

### Optimization

To find the optimal weight $w_j^*$, we take the derivative with respect to $w_j$ and set it to zero:

$$
\frac{\partial L(t_j)}{\partial w_j} = \sum_{i \in I_j} g_i + \left( \sum_{i \in I_j} h_i + \lambda \right) w_j = 0
$$
This is a form solution, using a higher degree gets us into quadratic or cubic for $w_{j}$, which is solvable. We have the exact solution for that, or we can use the Newton-Raphson method for solving it (which can be computationally expensive), but we it might yield better results. In the paper, it's a 2nd degree approximation.

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
L(t_j) = \left( - \sum_{i \in I_j} g_i \right)\frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda} + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) {\left(-\frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda}\right)}^2 + \gamma
$$


$$
L(t_j)^* = -\frac{1}{2} \frac{\left(\sum_{i \in I_j} g_i\right)^2}{\sum_{i \in I_j} h_i + \lambda} + \gamma
$$

This represents the **minimum loss value for a leaf** for a given tree structure. Since each data points lie in a single leaf. We can find the optimal value. 

---

## Split Criterion
At each node, we try different split values that try to minimize the splitting criterion. The split criterion for a node is defined similarly to the Information gain in a Decision tree.

$$
-\frac{1}{2} \frac{\left(\sum_{i \in I_(node)} g_i\right)^2}{\sum_{i \in I_(node)} h_i + \lambda} + \gamma - \left[ -\frac{1}{2} \frac{\left(\sum_{i \in I_(left)} g_i\right)^2}{\sum_{i \in I_(left)} h_i + \lambda} + \gamma -\frac{1}{2} \frac{\left(\sum_{i \in I_(right)} g_i\right)^2}{\sum_{i \in I_(right)} h_i + \lambda} + \gamma \right]
$$

$$
\frac{1}{2}\left[ \frac{\left(\sum_{i \in I_(right)} g_i\right)^2}{\sum_{i \in I_(right)} h_i + \lambda}  +  \frac{\left(\sum_{i \in I_(left)} g_i\right)^2}{\sum_{i \in I_(left)} h_i + \lambda} - \frac{\left(\sum_{i \in I_(node)} g_i\right)^2}{\sum_{i \in I_(node)} h_i + \lambda} \right]- \gamma
$$

where,  $n_(node) = n_(left) + n_(right)$

From observation, $\gamma$ does not play a role in optimal loss value, but it comes into the picture during gain calculation.

### Shrinkage and Column Subsampling

These are additional techniques to prevent overfitting, one is $\eta$ (learning rate), and column subsampling as we do in random forest. A lower value of max_features to be used in the Construction of the Decision Tree helps us in making models independent from each other. A group of independent models, even those with lower predictability, eventually yields good prediction power.

Let's visualize this. Suppose we have five independent weak classifiers, each having 0.6 accuracy. Suppose we are using voting to come up with a prediction value.

P(correct prediction) = P(3 models correct prediction) + P(4 models correct prediction) + P(5 models correct prediction)

Since each classifier is independent with accuracy $p = 0.6$, we use the binomial distribution:

$$P(k \text{ correct}) = \binom{n}{k} p^k (1-p)^{n-k}$$

Where:
- $n = 5$ (total number of classifiers)
- $k$ = number of correct predictions
- $p = 0.6$ (accuracy of each classifier)
- $(1-p) = 0.4$ (error rate)

***Case 1: Exactly 3 Classifiers Correct***

$$P(\text{3 correct}) = \binom{5}{3} (0.6)^3 (0.4)^2$$

$$P(\text{3 correct}) = 10 \times 0.216 \times 0.16 = 0.3456$$

***Case 2: Exactly 4 Classifiers Correct***

$$P(\text{4 correct}) = \binom{5}{4} (0.6)^4 (0.4)^1$$

$$P(\text{4 correct}) = 5 \times 0.1296 \times 0.4 = 0.2592$$

***Case 3: All 5 Classifiers Correct***

$$P(\text{5 correct}) = \binom{5}{5} (0.6)^5 (0.4)^0$$

$$P(\text{5 correct}) = 1 \times 0.07776 \times 1 = 0.07776$$

***Final Result***

$$P(\text{correct prediction}) = 0.3456 + 0.2592 + 0.07776 = 0.68256$$

Amazingly, we started with weaker models, but with the help of several independent weaker models, we collectively have better models.
Column Subsampling helps us to make the model independent.

$\eta$ is associated with the weightage given to each sequential gradient boosting tree that we are creating; a lower value gives less importance to individual trees and thus promotes the weightage of all the trees.

---

## More Optimization that makes XGBoost Scalable

### Approximate Split Finding Algorithm

XGBoost supports an approximate split finding algorithm to find the optimal split points, which speeds up training when we have millions of data points.
```
For each feature in 1 ... m:
    Use global or local split values based on percentiles
    for each value in the global or local percentiles values at node N
        Find split points that gives maximum gain as discussed in split criterion
```
This gives approximate split point at a Node
The paper proposes two ways of generating search space, (global and local)

Global is done once per feature for a particular tree, while local is done at each node level. Global is much faster (for each feature, percentile calculation is done once). 

The choice of global split values makes the training faster in comparison to local split values.


### Weighted Quantile Sketch
This Weighted Quantile Sketch Algorithms help us to find the split points even when the data doesn't fit in the memory.
And when data doesn't fit in memory, sorting is not possible and we can't use the approximate greedy algorithm. 

Weighted Quantile Sketch is a data structure that help us to find the split point by utilizing parallel computing, where each small chunk of data is handled independently.

This is done by assigning weights to each data point $x_{i}$, a weight of $w_{i}$, in our case, this is the Hessian $h_{i}$.

Weights is represented by hessian ($h_{i}$), Let's try to understand why we use $h_{i}$ as weights.

$$
L(t) = \sum_{i=1}^{n} \left[ g_i f_t(x_i) + \frac{1}{2} h_i f_t(x_i)^2 \right] + \Omega(f_t)
$$

$$
\frac{1}{2}h_{i}\sum_{i=1}^{n} \left[ 2 * \frac{g_i}{h_i} f_t(x_i) + f_t(x_i)^2 \right] + \Omega(f_t)
$$

$$
\frac{1}{2}\sum_{i=1}^{n} h_{i}\left[ 2 * \frac{g_i}{h_i} f_t(x_i) + f_t(x_i)^2 + \left(\frac{g_i}{h_i} \right)^2 - \left(\frac{g_i}{h_i} \right)^2 \right] + \Omega(f_t)
$$

$$
\frac{1}{2}\sum_{i=1}^{n} h_{i}\left[ \left(f_t(x_i) + \frac{g_i}{h_i} \right)^2\right] + \Omega(f_t) + \text{constant}
$$


We can see for each $x_{i}$, in loss function it is weighted by $h_{i}$, if 
$h_{i}$ is large, previous model is doing poor job in predictions.

This is done by creating quantile of equal weights of Hessian. There are lot of information provided in the Apendix section of the paper.

If we look at gain calculation, 
$$
Gain(Node) = \frac{G²}{(H + \lambda)}
$$

A higher $H$ represent lower gain. Lower gain means poor model performance.

Weighted Quantile is always preferred over simple Quantile, however this doesn't matter when we are using MSE as loss function, Hessian is constant here, For rest we always prefer weighted quantile as it tries to divide the data into blocks of equal hessian rather than doing by equal number of points. A low value of hessian is always preferred.

### Sparsity Aware Split Findings
By design, XGBoost can handle missing values, this is done by moving the data in the default direction, the default direction are learnt from the data.

For the missing values, it calculates the gain of sending them to the left branch versus the right branch. It then assigns the missing values to the direction that results in the higher gain. This becomes the default direction for that specific split and even used during the inference time.

## System Design

### Column Block for Parallel Learning


In XGBoost, we build the model sequentially; the process of building the second tree can only be achieved after we have built the first tree. There is no parallel processing involved here. 

We use parallel processing in finding the best candidate split.
This is done by using the Compressed Column Block. 

Before training, the dataset is partitioned by columns (features) into "blocks". Each block contains a subset of the features but all of the rows.
During the running of the best split, each thread runs its own best split on selected features and returns the respective information gain to the master thread.

XGBoost performs the expensive sorting only once, as a pre-processing step, before any trees are built.

***One-Time Setup***: For each feature F in the dataset, XGBoost creates a new data structure for it. This structure contains all the data points (or rather, their indices) sorted according to their value for feature F.

***Store in Blocks***: These pre-sorted lists are then stored in the columnar blocks we discussed earlier.

The column block saves the repeated sorting that needs to be done at each node for the best split.

Only the Hessian and derivatives need to be calculated at each sequential step; the calculation depends on its previous tree.

We can see 2 optimizations done here to fasten the process of split finding.

1. Parallel Processing to find the best split.
2. Pre-sorted data based on every feature F.

However, this is ***not a space-efficient solution***; we are consuming more space by doing the clever pre-sorting, equivalent to the data itself.

### Cache-aware Access



### Blocks for Out-of-core Computation




## Key Takeaways

1. **Taylor Approximation Enables Optimization** - Second-order Taylor expansion converts the problem into an easily solvable form. Optimal leaf weights have a simple closed-form solution depending only on gradients, Hessians, and regularization. Note that if we have taken a higher order approximation of Taylor's series, we might need to solve a quadratic equation to get the optimal value

2. **Leaf Independence** - The objective separates into independent leaf-wise losses, enabling efficient optimization

3. **Regularization in Action** - The $\lambda$ term in the denominator naturally prevents extreme leaf weights, controlling model complexity. Note $\gamma$ doesn't play role in the optimal value, but it plays important role in the optimal loss value, thus the split criterion

4. **Generality** - These formulas work for any differentiable loss function, making XGBoost applicable to regression, classification, and ranking tasks. We prefer a convex loss function.

---

## References

- Chen, T., & Guestrin, C. (2016). [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/pdf/1603.02754).

---

## Contributing

Found an error or have suggestions? Feel free to open an issue or submit a pull request!

