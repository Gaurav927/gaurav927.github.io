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
\frac{1}{2}\left( w^tw \right) \
$$

such that 

$$
y_{n}(w^tx_{n} + b) \ge 1
$$

`Using Langrange Multiplier`
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




