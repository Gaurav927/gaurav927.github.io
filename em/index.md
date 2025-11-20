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
$q_{i}(z_{i})$ = $p(z_{i} | \theta, x_{i})$, this is E step of EM algorithm



## M Step

Let us try to find the optimal value of $\theta$ at iteration t. Lets represent it by $\theta^t$. Putting the value of $\theta^t$, we get

$$
L(\theta^{t}, q_{i}) =  \log p(x_{i}|\theta^{t})
$$


$$
L(\theta^{t}) =  \sum_{i}^N\log p(x_{i}|\theta^{t})
$$

We will solve for optimal $\theta^{t}$ by usual MLE estimation, thus M step give us $\theta^{t}$



# Use case

Lets try to understand GMM, K-means and Probablitic PCA using the above setup.

## Gaussian Mixture Model

Lets suppose we are trying to fit k clusters each having $\mu_{i}$ and $\Sigma_{i}$ and $\pi_{i}$  

Consider MLE for GMM

$$
\sum_{i}^N \log(p(x_{i}|\theta))
$$

$$
\sum_{i}^N\log(\sum_{j}^k P(X = x_{i}, z_{i} = j | \theta)
$$

$$
\sum_{i}^N\log(\sum_{j}^k q(z_{i} = j) \frac{P(X = x_{i}, z_{i} = j | \theta)}{q(z_{i} = j)})
$$




$$
\sum_{i}^N\log(\sum_{j}^k q(z_{i} = j) \frac{P(X = x_{i}, z_{i} = j | \theta)}{q(z_{i} = j)})
$$

$$
P(X = x_{i}, z_{i} = j) = P(X=x_{i}| \theta, z_{i} = j)P(z_{i} = j| \theta, x_{i})
$$


`Using Jensen inequalities`


$$
\sum_{i}^N\sum_{j}^k q(z_{i} = j) log(\frac{P(X = x_{i}, z_{i} = j | \theta)}{q(z_{i} = j)})
$$

`using results from previous section`

$$
q_{i}(z_{i}) = p(z_{i} = j | \theta, x_{i})
$$

$$
p(z_{i} = j | \theta, x_{i}) = \gamma_{ij}
$$

`Using Bayes theorem`

$$
\gamma_{ij} = \frac{\pi_{j}N(x_{i}, \mu_{j}, \Sigma_{j})}{\sum\pi_{j}N(x_{i},\mu_{j}, \Sigma_{j})}
$$

This is E step, at each iteration, data point and number of cluster update $\gamma_{ik}$

`Lets goto M step`

$$
\sum_{i}^N\sum_{j}^k q(z_{i} = j) log(\frac{P(X = x_{i}, z_{i} = j | \theta)}{q(z_{i} = j)})
$$

$$
\sum_{i}^N\sum_{j}^k q(z_{i} = j) log(P(X = x_{i}, z_{i} = j | \theta)
$$


$q(z_{i} = j)$ doesn't depend on $\theta$

$$
\sum_{i}^N\sum_{j}^k \gamma_{ij} log(P(X = x_{i}, z_{i} = j | \theta)
$$





## Probablistic PCA


## K - Means

### Problem Defnition





