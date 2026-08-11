
mean field algo is an *approximate inference algorithm* for graphical models whose exact inference is __intractable__. 

The essence of this formalism can be better understood with parallels to physics:

Though exact calculations of the mechanics of a non ideal body (irregularly shaped, non uniform density) are not tractable, reinterpreting a body as a "cloud" of idealized point masses, and considering a sufficiently large amount of said point masses, does permit for dynamics to be formulated. This is an important foundation of statistical mechanics, as applied often in physics.

In the same spirit, the "mean field formalism" of feed forward, 2 layer Neural Networks (input, 1 hidden linear layer, output) conveys asymptotic behaviours of training dynamics and parameter configurations, allowing for analytic results, reasonable bounds & system complexity regarding a system that is, from other lenses,  idiosynchratic to the available data & learning task.

---

## Following what chat says

The more important notion is the way one thinks about parameter space, and gradient descent on said space. 

Consider the following setup: 

You have some network that takes in a (state) input X, passes it to a hidden layer, and outputs Y. 

let $m$ (it's $r$ in the case of [[liveVault/Optimal Control/Baros_etal_2025/Notes on Baros2025|Notes on Baros2025]]) be the # of neurons in the hidden layer. 
let $d$ be the dimension of the input layer (components of state)

then a fully connected feed forward, 2 layer network can be expressed as such: 

$$f(x ; \theta) = \frac{1}{m} \sum_{i = 1}^m a_{i}\sigma(w^\mathsf{T}x)$$


> [!note]  The $\frac{1}{m}$ factor is needed for the mean field formalism to make sense, as mean field theory studies asymptotic behaviours of N body systems, or in this case M neurons. So as $M \to \infty$, $f$ does not explode.

and if we let the weighing, biasing and non linear activation to be a single expression

$$\phi(x; \theta) = a_{i}\sigma(w^\mathsf{T}x)$$

then we can see that $f(x; \theta)$ is simply the average of $\phi(x; \theta)$ over the $m$ neurons of the network. Now it's more evident how mean field theory can help us better understand the black box function that is powering our feedback controller ([[baros2025 notes|Baros2025 notes]]).

Past the architecture, the training objective on N training samples is minimizing loss

$$\min_{\theta \in \Theta} L(\theta) $$
where 

$$
L(\theta)=\frac{1}{N}\sum_{i = 1}^N l(f(x_{i}, \theta), y_{i}) 
$$
and parameters $\theta = \{ w_{1}, a_{1}, w_{2}, a_{2}, \ldots, w_{m}, a_{m}\}$ are updated by

$$\theta^{k+1} = \theta^k - \eta \nabla_{\theta}L(\theta^k)$$
 
A very intuitive and commonplace view is to want to optimize for the loss of a parameter vector $\theta \in \Re^{m (d+1)}$. MFNNs opt for optimizing an *empirical (probability) measure*, reformulating a finite-dimensional problem into a infinite one. 

> [!note]- weird thing to do, right? make a finite problem infinite dimensional?
> One would feel inclined to say: why would you ever do that? The short answer is: to get some asymptotic guarantees about the training dynamics of NNs, which are notoriously black-box.
>  
>  (if not convinced, read more on __mechanistic interpretability [(1)](https://aarnphm.xyz/thoughts/mechanistic-interpretability#methodology), [(2)](https://www.anthropic.com/research/team/interpretability))

**Understanding Empirical Measures**

Given some $X$ with N components the *empirical measure* on $X$ is thus defined by the *Dirac delta measure*

$$\delta_{x}(A) = \begin{cases}
 1 & \text{if } x \in A \\
 0 & x \not\in A
\end{cases}$$

> This notation comes from statistical mechanics (physics), a more familiar concept in probability theory are indicator R.Vs:    $\delta_{x}(A) = \mathbb{1}_{X_{i} \in A}$

The corresponding empirical measure with samples from the true distribution $P$ is thus defined 

$$P_{\epsilon} = \frac{1}{N} \sum_{i = 1}^N \delta_{X_{i}}$$

which, in a more careless manner, is the same as

$P_\epsilon(A) = \frac{1}{N} \times \| \{\theta_{i} \in A\} \|$

> extensions exist for **weighted empirical measures**, where weights summing to 1 reflect importance sampling

it's important to remember that $\theta_i = (a, w)$, where a is a scalar and $w$ is a vector in $\mathrm{R}^d$.



> [!faq] Intuition behind suitability of a mean field formalism 
> The only issue I had with this formalism, which I couldn't put my finger on initially, is that intuitively, neuron parameter distributions should be idiosynchratic to the learning task / data, right? 
> 
> Though when you think about it, when learning an optimal feedback control, there is some configuration of parameters that lead to the best possible approximation of a best policy under uncertainty (call this $\theta^*$). And parameters in feed forward networks are completely permutable (all neurons fully connected), and so if we were to consider the empirical measure as $M \to \infty$, then **transporting** the measure $m^r$ (as a surrogate of $m^\infty$, the infinite analogue to $\theta^*$) to the _best_ measure $m^*$ under the cost constraint of [[Wasserstein Distance]] is in fact an admissable formulation of the inference & training dynamics of a 2-layer, feed forward network. 
> 



It is also noted that when $m \to \infty$, you can think of the system as arriving to the expectection of the output of a single neuron. Because when you think about it, there should really be no discernible difference among any two neurons, and working with regards to the expectation of a large population of neurons is exactly when we conceptually arrive at the mean field formalism.

Mathematically

$$\lim_{ m \to \infty } \frac{1}{m} \sum_{i = 1}^m \phi(x \; ;\theta) = \mathbb{E}_{x \sim \nu_{pop}}[\phi(x ; \theta)]$$

> [!summary] Implications for bounds in control learning dynamics for stochastic environments
> 
> Basically, we can put bounds via strong / weak LLN when viewing the output of one hidden layer ff NNs as a density of neurons across parameter space (where the empirical measure is an asymptotic approximation of $\vec{\theta}$, and arriving to an optimal loss is approximately estimating the optimal parameter measure).
> 
> 



Pter Caines - https://people.math.carleton.ca/~mhuang/MFGcaines.pdf