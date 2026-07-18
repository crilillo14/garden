
## 1. Intro

### Defining the problem 

An agent-environment learning problem distills down to four components > 
- policy
- reward signal
	- "defines the goal of a reinforcement problem"
	- environmetn gives agent a reward, agent tries to optimize for reward
	- a function of state and action ->  $g(s,u)$. this is cost in DP, but maximize reward is the same as min cost.
	- important to note: rewards are *near sighted*, and maximizing for rewards is how you get suboptimal greedy policies. Optimizing for long term *value* is the way that optimal policies are derived 
- value function
- (optional) model of the environment
	- model free interpretations are stronger as they levarage compute to approximate highly intractable environment evolutions / processes

> A primary issue when implementing optimal RL algos is the exploitation-exploration tradeoff

### A quick example : tic tac toe

State and control space is very easily defined. now for the value funnction: 
- terminal nodes (3 Xs, 3 Os) are very easy to define the value of (W / L)
- you approximate true value of a *certain state* (not control, this is an important distinction ), which more or less transaltes to the probability of winning [0 ,1] via *temporal difference learning*

$$ V(s) \leftarrow V(s) + \alpha(V(s') - V(s))$$

- left arrow is used to indicate an update; this is done many times across many games 

TTT is a game of PvP; RL extends to player vs Nature too. 

### History of RL 

Trial and Error, modern day RL -- throw data 

Optimal Control -- birthed in 1950 by Bellman
- these methods involve controls in an environment w state space $X$, finding a value function J and with corresponding operators T finding an iterative setup ot arrive to an optimal value.


## Preface to chapter 1: Tabular methods

This is basically what I think when I think of DP :

> To Bahman : "Understanding Policy Iteration, Value Iteration, and the general scope of what a DP problem was stumped me until I read the beginning of Sutton, Barto and reviewed tabular methods for RL. I also now understand that RL, optimal control and DP are tightly interwoven, though modern approaches to RL are trial and error and error driven, and that DP is a planning framework. Both tackle the issue of optimal control. "

past tabular methods, 3 methods for solving MDPs: 
- Dynamic Programming
- Monte Carlo methods
- TD learning

past singular introductions to these 3, ways in which they can be interwoven to get the best features out of them are discussed.
- MC + TD : eligibility traces
-  + DP (or other model learning and planning method) = complete and unified solution to tabular RL problem

## Chapter 2: Multi Arm Bandits

> In this book, Sutton + Barto do not put any stress in balancing exploration + exploitation in any elegant formulation, instead it's asking how to balance them at all (optimization vs existence). 
> This is due to the large scope of problems that RL seeks to encapsulate. 

problem formulation in short: bunch of levers, each with a stationary distribution of reward signals. maximize reward.

$q(a)$ : the true *expected* value of some action a. In this case a is pulling some lever in the action space

$Q_t(a)$ : is the estimated value of q(a) at t. 

$$Q_{t}(a) = \frac{R_{1} + R_{2} + \cdots + R_{N_{t}(a)}}{N_{t}(a)}$$

where $N_t(a)$ is the amount of times that a was chosen by the agent. 

it can be observed now that 

$$ \lim_{ t \to \infty } Q_{t}(a) = q(a)$$
as the mean of rewards over time will converge to the expected value of the unkown distribution (by law of large numbers ). known as the *sample average method* for action-value estimates.

If we want to be fully exploitative, we can always choose the argmax a of Q_t(a).

**$\varepsilon$-greedy** : pick unofrmly from A at time t, in an exploratory manner, $\varepsilon$ of the time.  $1 -\varepsilon$ of the time, go greedy.

> Asymptotically, this gurantees all actions are sampled infinitely, thus the optimal sol is guranteed. This doesn't say much about the effectiveness as we care about time-to-optimality.

epsilon greedy methods can never be 100% optimal *unless* epsilon tends to 0 as t goes to inf (exporation fades away as we are increasingly sure of the distribution $q(a)$). This is a personal remark, and not what the textbook says. 


This is a toy problem, where the action and state spaces are very simple to understand. There boviously exist tradeoffs with different problems, and strength and weaknesses to discuss. 

Greedy methods outperform eps greedy in a deterministic system, though this is usually not the problem people are tacking in RL.

The most common case in RL is that there exists *non stationarity* in the action value space