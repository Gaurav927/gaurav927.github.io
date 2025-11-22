# Support Vector Machines in Depth

## Problem Formulation

Classifying with good margin is always preferred over small margin

<img src="./image.png" alt="drawing" width="500"/>

## `Fiding W with large margin`
Let $x_{n}$ be the nearest point to the hyperpalne, $w^{T}$ x + b = 0.

Let  |$w^{T}$ $x_{n}$ + b| = 1

Lets take x' and x'' be two points lie on plane $w^{T}$ x + b = 0, note $w^{T}$ is a normal vector to plane.

$w^{T}$ x' + b = 0 \
$w^{T}$ x'' + b = 0

$w^{T}$(x' - x'') = 0

## Distance between $x_{n}$ and plane 

Lets define unit vector, the purpose of unit normal vector is to get perpendicular distance between plane and $x_{n}$
$$
\hat{w} = \frac{w}{|w|}
$$

$$
\frac{w^{t}(x_{n} - x')}{|w|}
$$

$$
\frac{w^{t}(x_{n} + b - x' - b)}{|w|}
$$

$$
max \left(\frac{1}{|w|}\right)
$$



If we maximize the distance between nearest point, we will get maximum margin classifier.

this is same as minimizing \
$|w|$ or $w^t w$
such that |$w^{T}$ $x_{n}$ + b| = 1


formally, we can write minimzation problem


$$
\frac{1}{2}( w^tw )
$$

such that 

$$
y_{n}(w^tx_{n} + b) \ge 1
$$

`Using Langrange Multiplier` \
this can be written as 

$$
\implies \frac{1}{2}\left( w^tw \right) \  - \sum_{i}^n \alpha_{i}\left[ y_{i}(w^tx_{i} + b)- 1 \right]
$$
subject to $\alpha_{i} \ge 0$

Lets take derivative w.r.t paramters L($\alpha$, w, b)

$$
\frac{\partial{L}}{\partial{b}} = \sum_{i}^n\alpha_{i}y_{i}
$$

$$
\frac{\partial{L}}{\partial{\alpha_{i}}} = y_{i}(w^tx_{i} + b)- 1
$$

$$
\frac{\partial{L}}{\partial{w}} = w - \sum_{i}^n \alpha_{i}y_{i}x_{i}
$$

For Optimal parameter, each derivative should be zero

$$
w = \sum_{i}^n \alpha_{i}y_{i}x_{i}
$$

$$
 \sum_{i}^n\alpha_{i}y_{i} = 0
$$


Putting values of above two eqn in Loss function

$$
\implies \frac{1}{2}\left( w^tw \right) \  - \sum_{i}^n \alpha_{i}\left[ y_{i}(w^tx_{i} + b)- 1 \right]
$$

$$
\implies \frac{1}{2}\left( w^tw \right) \  - \sum_{i}^n \alpha_{i}\left[ y_{i}(w^tx_{i} + b)- 1 \right]
$$


$$
\implies \sum_{i}^n \alpha_{i} - \frac{1}{2}\sum_{i}^n\sum_{j}^my_{i}y_{j}\alpha_{i}\alpha_{j}x_{i}x_{j}
$$

$$
\alpha_{i} \ge 0    \quad     and     \quad    \sum_{i}^n \alpha_{i} y_{i} = 0
$$
this is a function of α only

This can be written as quadratic programming

$$



\min_{\textcolor{blue}{\boldsymbol{\alpha}}}
\frac{1}{2} \textcolor{blue}{\boldsymbol{\alpha}}^\top
    
    \begin{bmatrix}
        y_1 y_1 \mathbf{x}_1^\top \mathbf{x}_1 & y_1 y_2 \mathbf{x}_1^\top \mathbf{x}_2 & \dots & y_1 y_N \mathbf{x}_1^\top \mathbf{x}_N \\
        y_2 y_1 \mathbf{x}_2^\top \mathbf{x}_1 & y_2 y_2 \mathbf{x}_2^\top \mathbf{x}_2 & \dots & y_2 y_N \mathbf{x}_2^\top \mathbf{x}_N \\
        \dots & \dots & \dots & \dots \\
        y_N y_1 \mathbf{x}_N^\top \mathbf{x}_1 & y_N y_2 \mathbf{x}_N^\top \mathbf{x}_2 & \dots & y_N y_N \mathbf{x}_N^\top \mathbf{x}_N
    \end{bmatrix}
    \textcolor{blue}{\boldsymbol{\alpha}} + 
    \underbrace{(-\mathbf{1}^\top)}_{\text{linear}} \textcolor{blue}{\boldsymbol{\alpha}} \\

$$

$$
\text{subject to} \quad 
\underbrace{\mathbf{y}^\top \textcolor{blue}{\boldsymbol{\alpha}} = 0}_{\text{linear constraint}} \\

$$

$$
\underbrace{\mathbf{0}}_{\text{lower bounds}} \le \textcolor{blue}{\boldsymbol{\alpha}} \le \underbrace{\boldsymbol{\infty}}_{\text{upper bounds}}
$$



$$
\alpha_{i}\left[ y_{i}(w^tx_{i} + b)- 1 \right] \ge 0
$$

for support vectors
$$
\alpha_{i} >0 
$$

Only support vectors define the margin, rest doesn't contribute to the decision boundary.

$$
w = \sum_{sv}\alpha_{i}y_{i}x_{i}
$$




This is important result between out of sample error rate and the number of support vectors, lower the number of support vectors, better the model.
This is the main reason SVM was very famous pre deep learning era, since it gurantees the error. this is geometric gurantee that model is not overfitting.


$$
\mathbb{E}[E_{\text{out}}] \leq \frac{\mathbb{E}[\# \text{ of SV's}]}{N - 1}
$$


Suppose we do a non linear transformation on x 

$$
L(\alpha) = \sum_{i}^n \alpha_{i} - \frac{1}{2}\sum_{i}^n\sum_{j}^my_{i}y_{j}\alpha_{i}\alpha_{j}z_{i}z_{j}
$$

The constraints remains same,

## Generalized ineer Product

Let's look at the beauty of Z transformation.
$$
\begin{align*}
    \textcolor{myviolet}{\mathbf{z}} &= \Phi(\mathbf{x}) = (1, x_1, x_2, x_1^2, x_2^2, x_1 x_2) \\[1.5em]
    K(\mathbf{x}, \mathbf{x}') &= \textcolor{myviolet}{\mathbf{z}}^\top \textcolor{myviolet}{\mathbf{z}}' = 1 + x_1 x'_1 + x_2 x'_2 + {} \\
    &\quad \quad \quad \quad \quad \quad x_1^2 {x'_1}^2 + x_2^2 {x'_2}^2 + x_1 x'_1 x_2 x'_2
\end{align*}
$$


$$
\begin{bmatrix}
    y_1 y_1 K(\mathbf{x}_1, \mathbf{x}_1) & y_1 y_2 K(\mathbf{x}_1, \mathbf{x}_2) & \dots & y_1 y_N K(\mathbf{x}_1, \mathbf{x}_N) \\
    y_2 y_1 K(\mathbf{x}_2, \mathbf{x}_1) & y_2 y_2 K(\mathbf{x}_2, \mathbf{x}_2) & \dots & y_2 y_N K(\mathbf{x}_2, \mathbf{x}_N) \\
    \dots & \dots & \dots & \dots \\
    y_N y_1 K(\mathbf{x}_N, \mathbf{x}_1) & y_N y_2 K(\mathbf{x}_N, \mathbf{x}_2) & \dots & y_N y_N K(\mathbf{x}_N, \mathbf{x}_N)
\end{bmatrix}
$$

rest everything remains same.



## The Optimization Problem (Soft Margin)


$$
\begin{aligned}
\text{Minimize} \quad & \frac{1}{2} \mathbf{w}^\top \mathbf{w} + \color{red}{C \sum_{n=1}^{N} \xi_n} \\[1em]
\text{subject to} \quad & y_n (\mathbf{w}^\top \mathbf{x}_n + b) \geq 1 \color{red}{- \xi_n} \quad \text{for } n = 1, \dots, N \\[1em]
\text{and} \quad & \color{red}{\xi_n \geq 0} \quad \text{for } n = 1, \dots, N \\[1em]
& \mathbf{w} \in \mathbb{R}^d, \quad b \in \mathbb{R}, \quad \color{red}{\boldsymbol{\xi} \in \mathbb{R}^N}
\end{aligned}
$$





We are trying to minimize the total voilations as well as maximize the margin by minimizing the w 

<img src="./image copy.png" alt="drawing" width="500"/>


$$
\begin{align*}
\text{\textbf{\Large Lagrange formulation}} \\[1em]
\mathcal{L}(\mathbf{w}, b, \boldsymbol{\xi}, \boldsymbol{\alpha}, \boldsymbol{\beta}) &= 
\frac{1}{2} \mathbf{w}^\top \mathbf{w} + C \sum_{n=1}^N \xi_n 
- \sum_{n=1}^N \alpha_n (y_n (\mathbf{w}^\top \mathbf{x}_n + b) - 1 + \xi_n) 
- \sum_{n=1}^N \beta_n \xi_n \\[1em]
\text{Minimize w.r.t. } \mathbf{w}, b, \boldsymbol{\xi} & \quad \text{and maximize w.r.t. each } \alpha_n \geq 0 \text{ and } \beta_n \geq 0 \\[1.5em]
\frac{\partial \mathcal{L}}{\partial \mathbf{w}} &= \mathbf{w} - \sum_{n=1}^N \alpha_n y_n \mathbf{x}_n = \mathbf{0} \\
\frac{\partial \mathcal{L}}{\partial b} &= - \sum_{n=1}^N \alpha_n y_n = 0 \\
\frac{\partial \mathcal{L}}{\partial \xi_n} &= C - \alpha_n - \beta_n = 0 \\
\frac{\partial \mathcal{L}}{\partial \alpha_n} &= -(y_n(\mathbf{w}^\top \mathbf{x}_n + b) - 1 + \xi_n)
\end{align*}
$$


Putting these values in our $\mathcal{L}(\mathbf{w}, b, \boldsymbol{\xi}, \boldsymbol{\alpha}, \boldsymbol{\beta})$

Look at 

$$
\sum_{n=1}^N C\xi_n - \sum_{n=1}^N \alpha_n \xi_n -\sum_{n=1}^N\beta_{n}\xi_n
$$

$$
 \sum_{n=1}^N \alpha_n y_{n} b
$$


these cancel each other