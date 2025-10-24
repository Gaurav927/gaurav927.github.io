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
$\hat{y_i}$ is prediction from the boosting model \
$y_i$ is actual label of the data \
Ω penalizes the complexity of the model \
T is the number of leaves in the tree \
w_i to represent score on i-th leaf

