# Expectation Maximization Algorithm

EM Algorithm is used to find optimal parameter of MLE or MAP when there is missing values or latent variables which makes it difficult to calculate MLE difficult.


## Derivation
$$
l(\theta) = \sum_{i=1}^{n} \log(p(x_{i} | \theta))
$$


$$
= \sum_{i=1}^{n}log\left[ \sum_{z_{i}} p(x_{i}, z_{i} | \theta)\right]
$$

Unfortunately, this is hard to optimize, since log can't be pushed inside the inner sum, this can be further written as 

$$
=\sum_{i}^N \log[\sum_{z_{i}}q(z_{i})\frac{p(x_{i}, z_{i} | \theta)}{q(z_{i})})]
$$


`Using Jensen inequality`

$$
\ge \sum_{i}\sum_{z_{i}}q(z_{i})\log(\frac{p(x_{i}, z_{i} | \theta)}{q(z_{i})})
$$

$$
Q({\theta, q}) \triangleq \sum_{i}\mathbb{E_{q_{i}}}[\log p(x_{i}, z_{i} | \theta)] + \mathbb{H}[q_{i}]
$$


the above arguments hold true for any valid distribution q. Which one should be choose? Intuitively, we should pick the q that gives us tightest lower bound.


$$
L(\theta, q_{i}) =  \sum_{z_{i}}q(z_{i})\log(\frac{p(x_{i}, z_{i} | \theta)}{q(z_{i})})
$$

$$
\sum_{z_{i}}q(z_{i})\log(\frac{p(z_{i} | \theta, x_{i}) p(x_{i}|\theta)}{q(z_{i})})

$$


$$
\sum_{z_{i}}q(z_{i})\log \frac{p(z_{i} | \theta, x_{i})}{q(z_{i})} + \sum_{z_{i}}q(z_{i})\log p(x_{i}|\theta)
$$

$$
\sum_{z_{i}}q(z_{i})\log \frac{p(z_{i} | \theta, x_{i})}{q(z_{i})} + \log p(x_{i}|\theta)
$$

$$
-\mathbb{KL}({q_{i}(z_{i}) || p(z_{i} | \theta, x_{i})}) + \log p(x_{i}|\theta)
$$

## E Step
$\mathbb{KL}$ is always greater than or equal to zero, the tighter bound will happend when 
$q_{i}(z_{i})$ = $p(z_{i} | \theta, x_{i})$, this is $\bold{E}$ step of EM algorithm

$$
L(\theta, q_{i}) =  \log p(x_{i}|\theta)
$$


$$
L(\theta) =  \sum_{i}^N\log p(x_{i}|\theta)
$$

## M Step

Let us try to find the optimal value of $\theta$ at iteration t. Lets represent it by $\theta^t$.







