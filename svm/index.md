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


## The Optimization Problem

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

$\alpha_{i} \ge 0$         and         $\sum_{i}^n \alpha_{i} y_{i}$ = 0

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

Only support vectors define the margin, rest doesn't contribute to the plane




