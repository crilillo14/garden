# Mean-Field Generalisation Bounds for Learning Controls in Stochastic Environments

 
## Introduction

__abstract__ : Data driven formulation of discrete time stochastic control -- mean field interpretation of single hidden layer NNs + DP principle = infinite dimensional minimization problems. Paper provides bounds ensuring stability of solution.
### Motivation

Model-based solutions to complex environments introduce model error, model free sols face curse of dimensionality. Thus finding deep policy networks for solving these problems are of interest. 

Problems that fall into this umbrella are aerial navigation & optimal investment for a *small* investor (a large investor would have effects on the system evolution)-
- highly dimensional
- may not be Markovian

Paper considers setting access to finite iid samples from trajectories in env
- with that, rather than fitting a parametrized statmodel to the samples, inevitably introducing model error, it opts to learn controls by direct optimization against these.


[[Reppen and Sonner]] -- shows that having an overparametrized model (NNs) leads to poor generalization
- leakage from train due to using the full trajectories -- learns to get ahead of train data rather than find underlying latents. 
- poor out of sample performance, good in sample performance not a good proxy for OOS error.

> Focus of this paper - stat learning issues when fitting to high dim stochastic env as outlined above. Serves as a framework on how to best fit a black box, overparametrized model for best-policy regression without sacrificing OOS performance

How does it do this? Via *entropy regularization*

Recasts prob as a backwards inductive series of inf dim minimisation problems. Gradient based training can then be understood in DP terms. 
- dont understand when it says that can be veiwed "as a dynamical system in the space of measures"

> [!info] It's important to remember this is an _analysis_ paper, not an ML one. 

## 1.2 Current Literature 

Because Sequential DM is in the interest of many intersecting sectors, any lit review would be incomplete when it comes to theory and computation.

**Theory**
- Existence & uniqueness of solutions 
	- Bertsekas & Shreve
	- extensions to weaken std assumptions of time consitency and markovian dynamics
		- [Hernandez and Possamai] 5
		- [Pham] 37
	- provide tools for computational methods, fails to address modelling. Sols may be intractable (Pham 36)
	- Issues lead to pursing statistical technuqies 

Simulation based iterative methods
- RL + approximate DP, ND programming, Monte Carlo methods. 
- rely on access to interactive access to stationary real world systems or pre calibrated simulators
	- performance deteriorates with dim


**In Practice**
- *Synthetic data generation of large time series models* is difficult as TS are often non stationary
	- Fu et al 16
	- solution: operate with small training sets
	- training generators with good general perf is  challenging
	- Motivation of this paper, learn HQ decision rules with little train data

**This paper's approach**
- parametrising controls with NNs as problem is high dim + nonlinear
	- good for blackbox function approximation + dim reduction
- Similar to NNcontPI method in [Huré et al] 26.
- As this is an analysis paper it analyzes training and OOS performance with a *mean field formalism*, though other methods exist
	- [[Rademacher complexity]]
	- [[neural tangent kernels]]
	- random feature models


Further Background: 
- [[mean field Langevin Dynamics]]
- [[Propagation of Chaos]]
- [[Entropy regulation]]

## 2. Problem Formulation

An empirical risk minimisation problem

A plane travels from one place to another, with random weather. Its utility is a function of travel time, fuel usage, and avoidance of obstacles.

The decision made per timestep is manuvering / accelerating. 

> "The approach we investigate exploits the fact that our control (the chosen velocity sequence) negligibly affects the weather. Therefore, we may view this part of the state vector as an uncontrolled process, the stochastic environment"

It's not weather mitigation, it's flight control. In other stochastic environments the agent may have a non negligible effect on the environment.

Find the control sequence satisfying 

$$\text{minimize } u \in C \mapsto \mathbb{E}_{Z \sim \nu_{\text{pop}}} \sum_{t=0}^{T-1} c_{t}(X_{t}^u(Z), u_{t}(X_{t}^u)) \:+ \Phi(X_{t}^u(Z)) $$
$$=: \mathbb{E}_{Z\sim \nu_{pop}} [l(X^u(Z), u)]$$
i.e. find the control sequence that minimizes the expected cost when sampling a random instance of the stochastic env from $\nu_{pop}$ of the trnasitionary costs (function of state, control and stoch env) up to the second to last step, plus the terminal cost dependent on only the final state.

$C$ is the space of feedback controls.

---
 **Notation / semantics** -- Relative Entropy is the same as Kullback Leibler Divergence, and has an explicit and very simple connection to cross entropy. Regularizing with KL div.

Also, this Baros builds from existing results from Propagation of Chaos in Mean Field Langevin Dynamics -- important to consult [[mean field Langevin Dynamics]] for background on this

---

**Assumptions of this framework >**

1. $$\forall u \in C ,\; X^u(Z) \; \text{ is a Markov process}$$
2. $$\text{Transition fns }\{h_t\}_{t=0}^{T-1}: X \times U \times Z \to X \text{ and costs } \Phi, c_{t} \text{ are known and continuous}$$
Assumption 2 is simple "niceness" of problem formulation, and #1 can be alternatively interpreted to mean that X is a sufficient statistic to describe state (this is not a POMDP).

> In the case of POMDPs, a derived "belief state" (probability distribution over $X$) would be a sufficient statistic. Any non markovian decision process can be, by enrichening the state space, markovian.

_On empirical measures and risk minimisation_

_def_  ERM: minimising an unbiased estimate of the expected loss
$$\arg \min_{u \in C} \quad  \mathbb{E}_{Z\sim \nu_{n}}[l(X^u(Z), u)]$$
--- 
### 2.2 Overlearning 

Authors cite a result from [[Reppen and Sonner]], where, when modelling the decision process of picking $u \in C$ as a 2-layer NN, overfitting to the training data (called *overlearning* in stochastic control literature) lead to poor out-of-sample generalization and in sample prediction rather than pattern recognition

> Past that, [[Reppen and Sonner]] also proves an asymptotic result _(eq. 4)_ that as the parameter space gets sufficiently rich (model becomes sufficiently __wide__) then the minimiser of the empirical risk outperforms the expected loss of the best control sequence, as well as the __expected__ loss of the best *anticipatory* control policy. Very cool stuff.
## 2.3 Control Parametrisation via Mean-Field Neural Networks

vocab from this section: 
- "rich" hypothesis space
	- when referring to measure parametrised controls and the reinterpretation of optimizing in $R^r$ 

==Scaled one hidden layer neural networks parametrize feedback control==
-Golse, Mei et al

let $r$ be the number of neurons in the hidden layer.

then the feedback control associated with the $r$ neuron network is $u_{\theta(r)} : X \to U$.

given some input vector $X$, we can get the specific control via

$$u_{\theta(r)}(X) = \frac{1}{r} \sum_{j=1}^r {a_{j}\sigma(w_{j}X + b_{j})}$$
if we let 

$$ \phi(\theta_{j}, X) = {a_{j}\sigma(w_{j}X + b_{j}}) $$

... turns out understanding the mean field formalism of Neural Networks stems from physics, optimal transport, and measure theory. All this is more or less tackled [[notes on Mean Field Networks|notes on Mean Field Networks]].

The most important notion to remember, from those notes:

> [!faq] Intuition behind suitability of a mean field formalism 
> The only issue I had with this formalism, which I couldn't put my finger on initially, is that intuitively, neuron parameter distributions should be idiosynchratic to the learning task / data, right? 
> 
> Though when you think about it, when learning an optimal feedback control, there is some configuration of parameters that lead to the best possible approximation of a best policy under uncertainty (call this $\theta^*$). And parameters in feed forward networks are completely permutable (all neurons fully connected), and so if we were to consider the empirical measure as $M \to \infty$, then **transporting** the measure $m^r$ (as a surrogate of $m^\infty$, the infinite analogue to $\theta^*$) to the _best_ measure $m^*$ under the cost constraint of [[Wasserstein Distance]] is in fact an admissable formulation of the inference & training dynamics of a 2-layer, feed forward network. 
> 


### notations / mean field formulation within Baros

Notations between Baros and the resources used to study mean field formalisms for NNs vary slightly, and it's important that for future discussion, these are very clearly defined. 

- $\theta^r$ is the $r$ neurons of the NN powering the control sequence. ==Every point in discrete time of the control sequence has a disticnt neural netowork==.
- control feedback parametrized by $r$ neurons : $u_{\theta(r)}$
- $\phi$ is the output of a single neuron

Given the empirical measure $m^r = \frac{1}{r} \sum_{j=1}^r \delta_{\theta_{j}}$

>then the activation of r neurons is equal to the integral of phi with respect to the integral $m^r$ over the parameter space $\Theta$.



When talking about the motivation behind expressing NN inference with respect to the empirical measure : "This allows us to view gradient-based training methods as dynamical systems in the space of probability measures over Θ, which we may analyse using infinite-dimensional calculus."

$$u_{m}(X) = \int_{\Theta} \phi(\theta, X) m(d\theta) = \mathbb{E}_{\theta \sim m}[\phi(\theta, X)]$$
This is the mean field formalism in its final form.

Going back to the highlighted portion, there is a distinct m^r for every t in 0, T. Though these are not related to each other (training will not be affected by past networks).
iterm
## 2.4 ADP & Entropy Regularization

let $P_{t}^m(x, dx')$ for t in T, x in $\chi$, and u in C, be the transition probabilities of the implied MDP (Not PO) of this decision process. 

should look more into this : pushforward notation of any arbitrary measurable function F, will probably be useful for regularization
- after inspecting, it states that $P(F(x)) =E[F(h(x, u, Z))] = E[F(X_{t+1}(Z) \mid X_{t} = x]$, which is more or less [LOTUS](https://en.wikipedia.org/wiki/Law_of_the_unconscious_statistician) for this specific [MDP](https://en.wikipedia.org/wiki/Markov_decision_process).
- used to state optimal value function at X_t+1 given X_t = x

[[hure25.pdf]] assumes MDP transition ufnction is time constant; Baros does not make such assumption; $Z \sim \nu_\text{pop}$ __possibly time inhomogenous__.

may solve for optimal value via the DP principle, given terminal conditions. Solving this gives $u_{m}^*$.


 > [!info] Remark 7 states that fitting _one_ NN as the answer to the entire control sequence over time simply leads you to exploding gradinent issues (Geron 19)


> Since this is a paper I'm reading _after_ going through [[Abstract DP, chapters 1 & 2]], the relevant DP theory seems pretty easy to follow, and is explicitly very faithful to Bertsekas' formulations / abstractions, though these might just be standard from Bellman.


__Throwing away transition probabilities, transition to empirical Q-functions__

Clearly we can't know the transition probabilities of _any_ of out state action pairs given the problem formulation, and hence we can't solve for an optimal control measure sequence as we can't find $Q_t$, more specifically the future expected optimal value that requires $P_{t}^m$ . Thus, estimating the cost to go Q functions with some N sampled Z from $\nu_{pop}$ is the best way to go.

_def_ 

$$\hat{Q_{t}}(x, m_{\text{t : T}} , Z \,) = \sum_{s \ge t} c_{s}^* (X_{s}^{t,x,m}(Z), m_{s})$$
semantically, the sum of running costs and the final terminal cost given some sample Z.

In [[hure25.pdf]], the ADP approach to finding an optimal control series is simply done by minimizing the empirical Q functions of $N$ samples of Z. In the measure controlled forumation of Baros et al, this behaves just as nice as with discrete action spaces, due to th Upper Triangular nature of minimisation via backwards induction (Given some Z i over t from 0 to T, one can always start from T and go back to 0, and will always be solving for exactly 1 unknown $m_t$).

> reference controls: When backward inducting at time t, the series of controls from t + 1 to T - 1.

> [!note] remark 8: the point of this paper is to have good OOS performance, implying better generalization of controls. The issue of more data is not relevant because even with a rich enough $X^{ref}$, it is impossible to expect that we can possibly explore __all__ possible states and perturbations at any point t from 0 to T -1. The more likely thing to happen is that we have T overfit NNs, and a very bulky and inefficient control sequence, 
> 
> THAT IS NOT __GURANTEED__ to converge to the optimal control measure sequence

In light of this shortcoming of pure ADP, and motivated by results in the setting of supervised learning, Baros introduces _entropy regularization_ to the chosen loss function 

AND SO, the revised minimisation, via backward induction, for every control $m_{t}$,  $t \in [0, T-1]$, is 

$$m_{t}\in \mathcal{P}_{2}(\Theta) \mapsto \mathbb{E}_{Z \sim \nu_{n}} [\hat{Q_{t}}(X_{t}^{\text{ref}}(Z), m_{t}, Z)] + \frac{\sigma^2}{2\beta^2}KL(m_{t} \|\gamma^{\sigma})$$

P2theta is L2 space over the parameter space.

$\sigma, \beta \gt 0$  are _regularization hyperparameters_. see [[#6]] for more

KL divergence is cross entropy - entropy of one of the measures


![[gibbsdef.png]]

>!!! I have no idea where this comes from and what this means !!! Pretty important considering you're punishing divergence from _some measure_ that I don't understand... 
>
>==update: == look at [[geneology of gibbs measures]]