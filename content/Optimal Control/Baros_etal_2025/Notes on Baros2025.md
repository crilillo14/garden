
"Mean-Field Gneralisation Bounds for Learning Controls in Stochastic Environments"

## Introduction

==abstract== : Data driven formulation of discrete time stochastic control -- mean field interpretation of single hidden layer NNs + DP principle = infinite dimensional minimization problems. Paper provides bounds ensuring stability of solution.
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

All in all, this is an analysis paper.


## 1.2 Current Literature 

Because Sequential DM is in the interest of many intersecting sectors, any lit review would be incomplete when it comes to theory and computation

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

_def_ ERM: minimising an unbiased estimate of the expected loss
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

where: 
- $a_j$ is the "output "