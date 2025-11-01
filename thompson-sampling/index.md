# Multi-Armed Bandit
Lets try to understand MAB using a case Dynamic Pricing for a ecommerce company. We want a new pricing for new product, this is a cold start problem.
No historical data available for it.

## Thompson Sampling

Thompson Sampling is mainly used for decisions problem where there is trade off exploration (invest in new knowledge) vs exploitation (make use of existing knowledge).

----
Example: Suppose there are K actions, and when played any action can yield success or failure. Action k $\in $ {1,...., K} produces success with probabililty $\theta_{k} \in [0, 1]$. The success probability ($\theta_{1}...\theta_{K}$) are unknown to the players. One way to learn these success probability is using experimentation. The objective, roughly speaking over T-periods is maximize the total number of success.

The arms might represent ads where we want to know the click through probability.
The objective of this blog is to understand when, why and how to apply Thompson Sampling.

### Thompson Sampling for Beta-Bernoulli Bandit
Lets continue with previous example, the mean rewards is unkown to us ($\theta$ vector). 
$$
p(\theta_{k}) = \frac{\gamma_(\alpha_{k} + \beta_{k})}{\gamma_(\alpha_{k})\gamma_(\beta_{k})} \theta_k^{\alpha_{k} - 1}{(1-\theta_{k})}^{\beta_{k} - 1}
$$

this is beta distribution $\beta_{k} (\alpha_{k}, \beta_{k})$.

Note $\beta (1, 1)$ is uniform distribution, and this is our prior beleif on each $\theta_{k}$, this is starting point. As we collect more information, we update our beleif, this is easy to to do since beta distribution is conjugate prior to bernoulli and binomial distribution. As observations are gathered we update the parameter using Bayes rule.

suppose we choose an arm k, the parameter is get updated every time arm k selected based on the rewards that we recieved based on success or failure.

$$
(\alpha_{k}, \beta_{k}) = (\alpha_{k} + r_{t},  \beta_{k} + 1 - r_{t})
$$

```
BernThompsonSampling(K, α, β)
for time = 1, 2 ..... do
    for k = 1 ...... K do
        sample θ ~ beta(αk, βk)

    xt = argmax (θ)
    apply xt and observe rt

    update distribution
    αk, βk = αk + rt, βk + 1 - rt
    
```







