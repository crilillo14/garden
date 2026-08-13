well, at the end of the day, one of the goals of reading this paper was to reproduce the results regarding the Zermelo and Merton problems, both which fall under the umbrella of stochastic optimal control problems with negligible agent influence on the environment (actions, or in this case control measures $m_t$ are independent of $Z_{t+1} \sim \nu_{pop}$)


### A quick note on the implementations of the outlined learning algorithm

At every $t \in [0. T-1]$ you are finding some _best_ $m_t$ that reduces the _empirical risk_ (consult back to teh beginning of [[baros2025 notes]] for a definition of the ERM).

There are two ERMs:

Unregularized
Regularized

__Section 6: Computational Aspects__ goes through the methodology (algorithm 1) of constructing the gibbs vector. 


## merton

Abstract: this is an _investment_ problem. performance is measured with profit

