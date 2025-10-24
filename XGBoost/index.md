# Learning nuts and bolt of XGboost
## Introduction
Gradient Tree boosting has gained a wide popularity in recent years, Its has been used in many Kaggle wining competetions. Here we going deep dive into understanding the Mathematics behind the working of Xgboost and whats makes it scalable.
Take the challenges hosted by the machine learning competition site Kaggle for example. Among
the 29 challenge winning solutions 3 published at Kaggle’s
blog during 2015, 17 solutions used XGBoost.

Here is link of the paper for the reference: [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/pdf/1603.02754)

We will first review 
1. Tree boosting and introduce a regularized objective
2. We then describe the split finding methods as well as the system design

### Fundamentals of tree boosting
Let D be our Dataset
$D = \{(x_i, y_i)\} \quad (|D| = n, \quad x_i \in \mathbb{R}^m, \quad y_i \in \mathbb{R})$


$$
\hat{y_i} = \phi(x_i) = \sum_{k=1}^K f_k(x_i), \quad f_k \in \mathcal{F}
$$

Loss function \
$L(\phi) = \sum_{i} l(\hat{y_i}, y_i) + \sum_{k} \Omega(f_k)$ \
$\Omega(f) = \gamma T + \frac{1}{2} \lambda \| w \|^2$

where \
L(ϕ) is the regularized objective function to minimize. \
$l(\hat{y_i}, y_i)$ is usually a convex function \
$\hat{y_i}$ is prediction from the boosting model \
$y_i$ is actual label of the data \
Ω penalizes the complexity of the model \
T is the number of leaves in the tree \
$w_i$ to represent score on i-th leaf

Note: We have a regularisation on the number of leaves for each Tree and the scores associated with it, but no regularisation on number of tree
When we set regularisation term to 0, it falls back to traditional gradient tree boosting.

### Gradient Tree Boosting
Let $f_{k}$ be kth tree that we are fitting in a sequential manner. The loss function for $f_{k}$ can be written as \

$L(t) = \sum_{i=1}^{n} l(y_{i}, \hat{y}_{i}^{(t-1)} + f_{t}(x_{i})) + \Omega(f_{t})$

Using taylor's series 2nd order approximation, expanding $l(y_{i}, \hat{y}_{i}^{(t-1)} + f_{t}(x_{i}))$ -------- eqn(1)

For any f(x, y + h), where h is delta change the taylors series can be written as \

$f(x, y + h) \approx f(x, y) + \frac{\partial f(x, y)}{\partial y} h + \frac{1}{2} \frac{\partial^2 f(x, y)}{\partial y^2} h^2$ ---- eqn(2)

comparing (1) and (2)

$$
x = y_i, y = \hat{y}_{i}^{(t-1)} and h = f_{t}(x_{i})
$$

$$
l(y_{i}, \hat{y}_{i}^{(t-1)} + f_{t}(x_{i}))
$$

the loss can be rewritten using taylors series 2nd degree approximation

$$
l(y_{i}, \hat{y}_{i}^{(t-1)}) + \frac{\partial l(y_{i}, \hat{y}_{i}^{(t-1)})}{\partial \hat{y}_{i}^{(t-1)}} f_{t}(x_{i}) + \frac{1}{2} \frac{\partial^2 l(y_{i}, \hat{y}_{i}^{(t-1)})}{\partial (\hat{y}_{i}^{(t-1)})^2} f_{t}(x_{i})^2
$$

$$
g_{i} = \frac{\partial l(y_{i}, \hat{y}_{i}^{(t-1)})}{\partial \hat{y}_{i}^{(t-1)}}
$$

$$
h_{i} = \frac{\partial^2 l(y_{i}, \hat{y}_{i}^{(t-1)})}{\partial (\hat{y}_{i}^{(t-1)})^2}
$$

So the loss can be rewritten as:

$$
l(y_{i}, \hat{y}_{i}^{(t-1)}) + g_{i} f_{t}(x_{i}) + \frac{1}{2} h_{i} f_{t}(x_{i})^2
$$


$L(t) = \sum_{i=1}^{n} \left[ g_i f_t(x_i) + \frac{1}{2} h_i f_t^2(x_i) \right] + \Omega(f_t)$


$$
\begin{align*}
\tilde{\mathcal{L}}^{(t)} &= \sum_{i=1}^{n} \left[ g_i f_t(\mathbf{x}_i) + \frac{1}{2} h_i f_t^2(\mathbf{x}_i) \right] + \gamma T + \frac{1}{2} \lambda \sum_{j=1}^{T} w_j^2 \\
&= \sum_{j=1}^{T} \left[ \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 \right] + \gamma T
\end{align*}
$$

$$
= \sum_{j=1}^{T} \left[ \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 + \gamma \right]
$$

$w_j$ is prediction in particular leaf, Its independent from the predictions from the other leaves, So 
$L(t) = \sum_{j=1}^{T} L(t_j)$

$L(t_j) = \left( \sum_{i \in I_j} g_i \right) w_j + \frac{1}{2} \left( \sum_{i \in I_j} h_i + \lambda \right) w_j^2 + \gamma$

Minimizing $L(t_j)$ with respect to $w_j$ gives 

$$
w_j^* = -\frac{\sum_{i\in I_j} g_i}{\sum_{i\in I_j} h_i + \lambda}
$$

where $g_i$ and $h_i$ depends on choice of loss function
















