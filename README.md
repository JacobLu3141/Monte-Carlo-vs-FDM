The introduction summarizes the content of this project quite well, so I will copy it here.

## 1. Introduction

### 1.1 The financial problem

A European call option gives its holder the right, but not the obligation, to buy an underlying asset at a fixed strike price $E$ and expiry date $T$. If $S_T$ is the asset price at expiry, the holder receives the payoff

$$\max(S_T - E\, 0).$$

We aim to find the theoretical fair price today, $V(S_0, t)$, given the spot price $S_0$, strike $E$, time to expiry $T-t$, volatility $\sigma$, risk-free rate $r$ and continuous dividend yield $D$.

The underlying is modelled as geometric Brownian motion. Under the risk-neutral measure $\mathbb{Q}$, we have

$$dS = (r-D)\,S\,dt + \sigma S\,dW^{\mathbb{Q}},$$

with the following two equivalent formulations.

**Probabilistic form (used in Task 1).** The price is the discounted expected payoff under $\mathbb{Q}$:

$$V(S,t) = e^{-r(T-t)}\,\mathbb{E}^{\mathbb{Q}}\big[\max(S_T-E,0)\big].$$

**PDE form (used in Task 2).** Delta hedging removes the risk, and $V$ satisfies the Black-Scholes equation

$$
\frac{\partial V}{\partial t} + \tfrac12\sigma^2 S^2\frac{\partial^2 V}{\partial S^2} + (r-D)S\frac{\partial V}{\partial S} - rV = 0, \qquad S\in[0,\infty),\; t\in(0,T)
$$

with the conditions

$$V(0,t)=0,\qquad \lim_{S\to\infty}V(S,t)\sim S,\qquad V(S,T)=\max(S-E,0).$$

The last condition is a terminal condition, since the solution is known at expiry and required today. Substituting $\tau = T-t$ turns this into a marching forward problem from $\tau=0$.

For constant coefficients, the Black-Scholes formula is a closed-form solution. We use it as the reference for every error reported in this notebook.

### 1.2 Numerical procedures

**Monte Carlo (Task 1).** We simulate $N$ paths of $S$ with the Euler-Maruyama scheme,

$$S_{n+1} = S_n + (r-D)S_n\delta t + \sigma S_n\sqrt{\delta t}\,Z_n,\qquad Z_n\sim\mathcal N(0,1),$$

then evaluate the payoff on each path, average, and discount. The error due to using $N$ realizations is $O(1/\sqrt{N})$, and the discretization error is $O(\delta t)$.

**Explicit finite difference method (Task 2).** We discretize the PDE on a grid in $(S,t)$ with $S_i = i\,\delta S$ and $t_k = T - k\,\delta t$ on a truncated domain $[0,S^*]$. We replace each derivative by a Taylor-series approximation, which gives an update of the form

$$V_i^{k+1} = \alpha_i V_{i-1}^k + \beta_i V_i^k + \gamma_i V_{i+1}^k.$$

Each new value depends only on known values from the previous time level, so the scheme is explicit and marches forward from the payoff. The error is $O(\delta t, \delta S^2)$, and its stability is conditional on $\delta t < \frac{1}{\sigma^2 I^2}$, i.e. $\delta t = O(I^{-2})$, which is investigated in Section 6.

### 1.3 Aims and structure

We aim to:  
* (i) implement both methods and validate them against the closed form, 
* (ii) study how the option price responds to the input data, 
* (iii) measure the order of accuracy of the explicit scheme, 
* (iv) demonstrate what happens when the stability condition is violated, and 
* (v) compare the two methods in accuracy.

We structure our report as follows: 
* Section 2 sets up the environment and benchmark, 
* Sections 3 and 4 cover Tasks 1 and 2, 
* Sections 5 and 6 cover the accuracy and stability studies, and 
* Sections 7 and 8 give observations and conclusions.
