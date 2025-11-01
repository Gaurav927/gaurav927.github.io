# Multi-Armed Bandit
Lets try to understand MAB using a case Dynamic Pricing for a ecommerce company. We want a new pricing for new product, this is a cold start problem.
No historical data available for it.

## Thompson Sampling

Thompson Sampling is mainly used for decisions problem where there is trade off exploration (invest in new knowledge) vs exploitation (make use of existing knowledge).

----
Example: Suppose there are K actions, and when played any action can yield success or failure. Action k $\in $ {1,...., K} produces success with probabililty $\theta_{k} \in [0, 1]$. The success probability ($\theta_{1}...\theta_{K}$) are unknown to the players. One way to learn these success probability is using experimentation. The objective, roughly speaking over T-periods is maximize the total number of success.

The arms might represent ads where we want to know the click through probability.
The objective of this blog is to understand when, why and how to apply Thompson Sampling.

### Greedy Algorithms 
This is simple solution to Bandit Problem.




