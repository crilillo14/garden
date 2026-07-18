
mean field algo is an *approximate inference algorithm* for graphical models whose exact inference is __intractable__. 

The essence of this formalism can be better understood with parallels to physics:

Though exact calculations of the mechanics of a non ideal body (irregularly shaped, non uniform density) are not tractable, reinterpreting a body as a cloud of idealized point masses which 

---

## Skipping the paper (overly formalized)

The more important notion is the way one thinks about parameter space, and gradient descent on said space. 

With some std 2 layer NN, consider the parameter vector $$\theta = \{\theta_{1}, \theta_{2}, \ldots, \theta_{N}\}$$
A very intuitive and commonplace view is to want to optimize for loss in parameter space $\Re^N$. MFNNs are alternatively interpreted with *empirical measures* .

**Understanding Empirical Measures**

Given some $X$ with N components the *empirical measure* on $X$ is thus defined by the *Dirac delta measure*

$$\delta_{x}(a) = \begin{cases}
 1 & \text{if a = x} \\
 0 & \text{otherwise}
\end{cases}$$

