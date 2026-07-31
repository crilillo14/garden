 

## Chapter 1: Introduction 

*A deterministic optimal control example*

- Page 4, top: transition from instances of example J cost functions, but then when discussing operators on J, the attached policy is dropped because its only important that J as a function space is considered
	- is this shit functional analysis? NOt really, but more so abstracting the machinery of control theory.
- Since the system is deterministic, the T operator (cost updater with one step lookahead) is trivial for the fn space J : X -> R, 
	- aka J_mu =T_mu J_mu for any policy mu.
- why should i even give a fuck about the T operator? Seems overcomplicated
	- Answer: fixed value problem on an operator == optimization. 
	- for the sake of the algos
- Clarification on whether I understand the reformulation of an infinite horizon problem in terms of T transforms
	- I think it's because there makes no sense for there to be terminal states, thus no sense to have terminal value funnctions that are non zero

*1.2 Abstract DP Models*

> Bertsekas will often discuss stationary policies more frequently, and will discuss a policy
> $$\mu \in M$$
> When discussing **any** policy, unless there is need to discern between stationary and non stationary policies. (Stationary := $\pi = \{\mu, \mu, \ldots \}$) 

*1.2.2 Monotonicity & Contraction Properties*

For basically chapter 2, Bertsekas assumes these two properties about $J : X \mapsto R$. 

**ALSO**,
$$T, T_{\mu} : R(X) \mapsto R(X)$$
is an assumption we will be cool with up until later. Then we will discuss operators that work in $$R^*(X) = \Re \cup \{- \infty , \infty\}$$ 
> Important to remember: monotonicity and contraction are properties of the **bellman backup operator**, not the value function $J$.

### Intermission, and a slight overview of fixed point contraction mappings

Following Appendix B.

Super duper important things to discuss: 
- lowkey understanding how J is a cauchy sequence under the contractive mapping T. 
- completeness of a vector space, and when its not complete.


I think I understood complete spaces, cuachy sequences, and how it maps back to T being a contraction mapping and J* being a fixed point of the mapping.



*1.2.3 Some Examples*

**1. Stochastic Optimal Control - Markovian Decision Problems**

Doubts - 
- measure theoretic shit, extended reals, and the expected val of the N stage cost being Real. 
	- why does the noise coming from a countable set imply that the sum is possibly countably infinite sum

We can impose assumption on $\alpha$ and $g$ to work with the $\\lim_{ N \to \infty }$ and not $\lim \sup_{N \to \infty}$.
- $\alpha \le 1$
- $\mathbb{E}_{w} \, g(x,u,w)$ bounded above by some $B \in \mathbb{R}, \quad \forall x \in X$ .

then 


$$ J_\pi (x_{0}) = \lim_{ N \to \infty } \mathbb{E}_{w_{k}} \left\{ \sum_{k=1}^{N} {\alpha^k g(x_{k}, \mu_{k}(x_{k}), w_{k})} \right\}$$
$$ k = 0, 1, 2, ...$$

Just like deterministic optimal control but wiht expected val. Straightforward if $w_{k}\perp w_{k+1}$ (independent), more complicated if sources of randomness are more strictly tied to system state.

**2. Finite-State Discounted Markovian Decision Problems**

For stochastic systems with finite states, the system eq. may be defd by transition probabilities (like a markov chain)
$$p_{xy}(u) = P(y = f(x,u,w) \mid x)$$

and then, you get a weighted sum of the one step lookahead cost with respect to the above distr. 


$$H(x,u,J) = \sum_{y \in X} p_{xy}(u)(g(x,u,w) + \alpha J(y))$$

discounted finite state MDP -- Applied to modelling continuous probs by discretization.

**3. Discounted Semi Markov**

contractive with respect to inf norm

Didnt really get this one. Dont think its too importnat though.

According to chat:

A semi-Markov process is specified by

1. Transition probabilities
2. Holding time distributions

Basically, instead of having transitions at every discrete time, or  jumping following an exponential distribution, semi-markov processes have holding time distributions different from exp.

> Should revisit this section later.

**4. Discounted Zero Sum Dynamic Games**

basically dynamic minimax with evolving states.

P1 chooses to minimize via some control $u$, while P2 chooses a control $v$ that maximizes P1's cost. This is due to zero sum. 

let A be the payoff matrix, n x m, where P1 and 2 have n and m choices for controls. 


> Past this point, just skimming.
>  Minimax, SSPs, DSPs, Multiplicative and Risk Sensitive models are also talked about, but are honestly discussed too briefly to get anything valueable. Important to read back when reading later chapters. 
> 
> SSPs and DSPs are graph problems. Multiplicative models multiply the state costs, such that minor inefficiencies in controls cause huge spikes in cost.
> Completely skipping aggregation


### 1.3 Reinforcement Learning..

_Not explored; is not central to the understanding of chapter 2. Should be, again, revisited when touched upon_


*1.3.2 Approximation in Val Space, Newton's Method*




## Chapter 2: Contractive Models

$J \le TJ$ - just because your cost is going to be equal to or worse to your cost on the next step by taking the optimal control u.

for the weighted sup norm $$\lvert \lvert J  \rvert  \rvert = \sup_{x  \in X} \frac{\lvert J(x) \rvert }{v(x)} $$
it's important to note that $v(x)$ semantically is a weighing of expected cost per state. Like if your process is getting on a flight, and states are paying for a flight and also paying for a snack, then there are some expected monetary values that should be around. paying 100$ for a snack when you should spend 5$ and paying 600$ for a flight that usually costs $500 isnt the same, in terms of cost. its just a weighing in terms of the total allocation of cost permitted by your system. 

also, defining $B(X)$ is like some butcher shit, just getting rid of the fat (functions $J: X \to R$ that don't make sense).

*Q: is the $\alpha$ denoted in Prop 2.1.1 the discount factor or is it modulus that exists in (0,1)? *
A: Yes, it's the discount factor. 


monotonicity, when it holds along with contractivity, implies that 

$$J^*(x) = \inf_{\mu \in M} J_{\mu}(x) \qquad\forall x \in X$$


it can be proven that 

$$
\exists \,\mu \in M \qquad J^*(x) \le J_{\mu_{\epsilon}(x)} \le J^*(x) + \epsilon
$$

**Optimality over Nonstationary Policies**

now, let's consider when J is less tractable.

$$J_{\pi} = \lim \sup_{ N \to \infty }  T_{\mu_{0}}T_{\mu_{1}}T_{\mu_{1}}\cdots T_{\mu_{N-1}}\bar{J}(x) \qquad \forall x \in X$$

> Important :::: Under monotonicity, minimizing over U(X) for $J_\mu$ was the way to get $J^*$, but with nonstationary policies you minimize over $\Pi$. In fact, $M \subset \Pi$.

BUT 

> Even if the optimal policy is wildly nonstationary, there is always a single stationary policy whose cost is arbitrarily close to optimal.

REMEMBER 


$$
\exists \,\mu \in M \qquad J^*(x) \le J_{\mu_{\epsilon}(x)} \le J^*(x) + \epsilon
$$

so there is always shit in $M$ that is good enough **over time.**

... A bunch of shit on error bounds and one step and multi step lookahead policies . 


**Finishing chapter 2: after reading the first 30 pages of RL by Sutton and Barto**

> An imporatnt notion that will fundamentally divide RL and DP is that DP is a much stricter "model centric" framework in which to solve an optimal control problem. RL deviates by levaraging large amounts of data to approximate a fuzzy estimate of $T$, $J$, $J^*$, and any operators and functions found within Bellman's Equation.
> Q learning, TD, and everything in RL is just assuming we don't have a $T$ bellman lookup operator with which we can conduct policy iteration & value iteration.

Since RL and DP are very interlinked, but usually treated as two separate fields, synonyms exist across them:

- self learning = policy iteration
- policy evaluation done via NNs

This book is really abstract in terms of its formulations, and it is precisely abstraction that is its goal. It's very important to couple this with other more boots on the ground textbooks, videos and example toy problems that can better illustrate what the abstract framework provided is saying. 

OK, now done with this prelude in the middle of notetaking for Ch 2. Back to content. 


### 2.2 Limited Lookahead policies

given some $\tilde J$ that approximates $J^*$, we can solve a finite horizon problem where $\tilde J$ is the terminal cost function. 

AKA: J* is intractable, but with discounting it's important to note that cost converges to a single value for all x in X. Thus, we can pick a suitable k > 0 to be the cutoff terminal horizon for the approximating cost function $\tilde J$, hence the name *limited lookahead*.

- one step lookahead policy $\bar \mu$: solving for the next greedy minimizing action. Assuming $\bar J$ is a suitable approximation for all future cost. 

... A lot of analysis results on the modulus of contraction and error bounds when applying oen step lookahead in this scenario. $\varepsilon$ arguments and tightness of bounds

*A simple 2 state example*

Say state 1 has 2 possible actions
state 2 is absorbing / terminal

move to state 2 = $\mu ^*$
stay at 1 = $\mu$

g (x, \mu) = $2\alpha\varepsilon$, cost is 0 for every other control

optimal cost is to go to 2 right away -> $J^*(1) = J^*(2) = 0$ 

let $$\tilde J (x) =  \begin{cases}
-\varepsilon &\text{if $x = 1$} \\
\varepsilon &\text{if $x = 2$} \\
\end{cases}
$$
it can then be seen that the analytic bounds of moduli for the contraction of J holds with exact equality. $\forall \varepsilon \in \mathbb{R}$. 


*Multistep lookahead policies w/ approximations*

given any J in B(x), we can't exactly compute TJ, but we can compute $\tilde J$ in B(x) and a stationary policy in M such that: 

$\tilde J$ is at most delta away from TJ, and $T_{\mu}J$ is at most epsilon away from TJ.
- delta, epsilon unkonown, so these results are more qualitative. 
- both the policy and approximate optimal cost function can be found by fitting noisy valeus of TJ from simulation

now, for the multistep lookahead, 
we can, given some terminal cost fn $J_m$, we can succesively compute Js and mus that satisfy the strict delta epsilon error bounds. When m = 1, the error bounds computed prior arise.

> Okay, now one last time, an aside about the goal of this textbook. Getting any concrete methodology to solve DP problems is the wrong mindset; instead, it talks about the more so general properties of type of DP problems surroundign the Bellman Equation, giving error bounds, correctness, and ultimately the efficiency and viability of certain scenarios that peole run into when working on problems with intractable / unkown value functions.


as $m \rightarrow \infty$, the error of $J_\pi$ from the optimal cost fn goes to

$$\limsup_{ m \to \infty } \|J_{\pi}-J^*\| \le \frac{{\epsilon + 2\alpha\delta}}{1- \alpha}$$
>For online lookahead (MPC) no error bound is known


### 2.3 Value Iteration

Big scope: find a sufficiently good approximation of J*,  and then optimize H over all x to get $\bar \mu$.

It can be proven that if the set of stationary policies M is finite, then there is some k > 0 such taht ... basically the optimal policy is tractable


let $$\tilde M = \{J \in B(x) \mid J_\mu \neq J^*\}$$
it follows obviously that the inf over M tilde of the error from J* is greater than 0. 

*2.3.1 Approximate Value Iteration 

> Overarching question for approximate methods: How do you ctually verify taht you are delta or epsilon away from TJ? Or are you simply assuming there is some error / you do not have the optimal cost fn?

Start from $J_0$

{ $J_{\mu^0}, J_{\mu^1}, \ldots, J_{\mu^k}$}, with J mu k tending to J.

*Counterexample: Err amplification*

say you only accept value funcitons of the family r, 2r. 

We have the familiar 2 state problem. The optimal cost fn actually only takes into account J(2), the terminal state. J* = $(2\alpha J(2), 2\alpha J(2))$.

But if we were to only accept a subspace of R2 as approximate functions, then value iteration has a least squares projection too. 

GIven the solution, we know that if alpha is sufficiently large, then the modulus of the mapping $\Pi T$ is actually > 1, and the contraction then becomes an explosion, and then the delta epsilon bounds on the iteration steps fall apart

> Important to note: This is why the anlytic machinery of abstract DP is important. The general "problem" needs to fit into these bounds to be able to apply fixed point methods. 


### 2.4 Policy Iteration

Given a current policy ${\mu^k}$

*Evaluation*

compute $J_{\mu^k}$ as the fixed point of $T_{\mu^k}$, the one step lookahead operator of the policy. We're simply finding the value function of the policy.

*Improvement*

Choose a better policy $\mu^{k+1}$ greedily :
$$T_{\mu^{k+1}}J_{\mu^k} = TJ_{\mu^k}$$

where $\mu^{k+1}(x) = \arg \min_{u \in U(x) } H(x, u, J_{\mu^k})$ for all x in X.



For infinite sets of policies, convergence can be insured with some compactness and continuity conditions.

If they are allowed, then the *limit point* $\bar \mu$ of $\{\mu_k\}$ will satisfy $J_{\bar\mu} = J^*$.

**2.4.1 Approximate PI**

same epsilon delta bounds, but now for evaluation and improvement steps

*2.4.2 Approximate PI where policies converge*

may also use *exact PI* with a different mapping $\tilde H$, satisfying the error bounds outlined in 2.4.1. This mapping can be derived via aggregation or some other method.


## 2.5 Optimistic PI and $\lambda$-PI

Approximating Policy Evaluation using Value Iteration. 

*Optimistic PI* : evaluated using a finite number of VI.

$\lambda$-PI is trickier , but follows the same idea. VERY similar to TD($\lambda$)




