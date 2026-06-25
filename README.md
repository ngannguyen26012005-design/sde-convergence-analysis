# Convergence Analysis of Numerical Methods for Stochastic Differential Equations

A self-contained Python project empirically validating the **strong convergence properties** of the Euler–Maruyama and Milstein numerical schemes for stochastic
differential equations (SDEs), applied to Geometric Brownian Motion (GBM).

This project was developed as an independent computational mathematics study, combining stochastic calculus, numerical analysis, and computational finance.

## Research Question

> Do the Euler–Maruyama and Milstein schemes empirically achieve their
> theoretical strong convergence rates of order 0.5 and 1.0 respectively
> when applied to Geometric Brownian Motion, and how do their accuracy and
> computational cost compare as a function of step size?

## Results at a Glance

| Method | Theoretical Strong Order | Empirical Order |
|---|:---:|:---:|
| Euler–Maruyama | 0.500 | **0.4983** |
| Milstein | 1.000 | **0.9980** |

Both schemes closely match their theoretical convergence rates, confirming the correctness of the numerical implementation and experimental design.

# Mathematical Background

## Geometric Brownian Motion

The asset price process is modelled by the stochastic differential equation:

$$
dS_t = \mu S_t\,dt + \sigma S_t\,dW_t
$$

where:

- $\mu$ is the drift coefficient
- $\sigma$ is the volatility
- $W_t$ is a standard Brownian motion

Applying Itô's Lemma to $\ln(S_t)$ gives the analytical solution:

$$
S_T =
S_0
\exp
\left(
(\mu-\frac{1}{2}\sigma^2)T+\sigma W_T
\right)
$$

This exact solution provides a **reference solution** for evaluating numerical approximation errors.

## Numerical Schemes

### Euler–Maruyama Method

Euler–Maruyama discretises the SDE directly:

$$
S_{n+1}
=
S_n
+
\mu S_n \Delta t
+
\sigma S_n \Delta W_n
$$

with theoretical strong convergence order:

$$
O(\Delta t^{0.5})
$$

### Milstein Method

Milstein improves Euler–Maruyama by adding an Itô correction term:

$$
S_{n+1}
=
S_n
+
\mu S_n\Delta t
+
\sigma S_n\Delta W_n
+
\frac12\sigma^2S_n
((\Delta W_n)^2-\Delta t)
$$

with theoretical strong convergence order:

$$
O(\Delta t)
$$

The correction term improves pathwise accuracy by incorporating information from the variation of the diffusion coefficient.

## Strong and Weak Convergence

| Type | Definition | Meaning |
|---|---|---|
| Strong convergence | $\mathbb{E}[|S_T^{exact}-S_T^{num}|]=O(\Delta t^\gamma)$ | Measures pathwise accuracy using the same Brownian trajectory |
| Weak convergence | $|\mathbb{E}[g(S_T^{exact})]-\mathbb{E}[g(S_T^{num})]|=O(\Delta t^\beta)$ | Measures accuracy of expectations such as option prices |

For GBM, both Euler–Maruyama and Milstein achieve weak convergence order 1.0
under standard assumptions.


# Experimental Design

The main challenge in measuring strong convergence is ensuring that the exact solution and numerical schemes use the **same Brownian motion realization**.

Following the framework of Higham (2001), the experiment uses a shared Brownian path across all discretization levels.

## Procedure

1. Generate a fine Brownian grid:

$$
\delta t = \frac{T}{N_{fine}}
$$

with:

$$
N_{fine}=2^{12}=4096
$$


2. Compute the reference GBM solution using:

$$
W_T=\sum_i \Delta W_i
$$


3. Construct coarser time steps by aggregating fine Brownian increments:

$$
\Delta W_{coarse}
=
\sum_{i=1}^{R}\Delta W_i
$$


4. Simulate Euler–Maruyama and Milstein paths.

5. Compute the mean strong error:

$$
Error
=
\frac1M
\sum_{j=1}^{M}
|
S_T^{exact}
-
S_T^{numerical}
|
$$


6. Plot:

$$
\log(Error)
\quad\text{against}\quad
\log(\Delta t)
$$

The slope of the regression line estimates the empirical convergence order.

## Parameters

| Parameter | Value | Description |
|---|---|---|
| $S_0$ | 100 | Initial asset price |
| $\mu$ | 0.10 | Drift |
| $\sigma$ | 0.20 | Volatility |
| $T$ | 1.0 | Simulation horizon |
| $M$ | 10,000 | Monte Carlo paths |
| $N_{fine}$ | 4096 | Fine Brownian grid |

# Project Structure

This project is implemented as a single self-contained Jupyter Notebook.

| Section | Content |
|-|-|
| Mathematical Background | GBM, Itô's Lemma, convergence theory |
| Numerical Methods | Euler–Maruyama and Milstein schemes |
| Convergence Experiment | Error analysis and convergence estimation |
| Financial Application | European option pricing validation |
| Conclusion | Findings and limitations |


# Key Findings

## 1. Strong Convergence Verification

The observed convergence rates were:

| Method | Empirical Order | Theory |
|-|-:|-:|
| Euler–Maruyama | **0.4983** | 0.5 |
| Milstein | **0.9980** | 1.0 |

The numerical experiments confirm that both schemes achieve their expected strong convergence behaviour.


## 2. Accuracy Comparison

Milstein consistently produces lower pathwise errors than Euler–Maruyama across all tested discretization levels.

Because Milstein has a higher convergence order, it reaches a given accuracy level using fewer time steps.

For a target error $\epsilon$:

| Method | Required Step Size |
|-|-|
| Euler–Maruyama | $O(\epsilon^2)$ |
| Milstein | $O(\epsilon)$ |

This demonstrates the computational advantage of higher-order numerical schemes.


# Financial Application: European Option Pricing

The validated simulations were applied to European call option pricing.

Parameters:

- Strike price: $K=100$
- Risk-free rate: $r=0.05$
- Paths: $100,000$
- Time steps: $N=256$

| Method | Price | Absolute Error |
|-|-:|-:|
| Black–Scholes analytical | 10.4506 | — |
| Exact GBM Monte Carlo | 10.3909 | 0.0597 |
| Euler–Maruyama Monte Carlo | 10.3248 | 0.1258 |

The exact GBM Monte Carlo result differs from the Black–Scholes value mainly due to Monte Carlo sampling variation.

For the chosen discretization level, the Euler–Maruyama estimate shows a small detectable discretisation bias, demonstrating that numerical approximation error
remains present at finite step sizes.


# Conclusion and Limitations

This project experimentally verified the theoretical strong convergence rates of two classical numerical schemes for stochastic differential equations.

The Euler–Maruyama method achieved approximately:

$$
O(\Delta t^{0.5})
$$

while Milstein achieved approximately:

$$
O(\Delta t)
$$

The results confirm that Milstein provides improved pathwise accuracy due to its
higher-order correction term.

The project also demonstrated how numerical SDE methods can be applied to
computational finance through European option pricing.

## Limitations

- The model is restricted to Geometric Brownian Motion, a linear SDE with constant coefficients.
- Real financial markets exhibit volatility clustering, jumps, and heavy tails not captured by GBM.
- Weak convergence analysis was not explored in depth.
- Extension to multidimensional SDEs would require additional techniques such as Lévy area simulation.

Future extensions could include stochastic volatility models such as the Heston model or more advanced Monte Carlo methods.

# References

- Higham, D.J. (2001).
  *An Algorithmic Introduction to Numerical Simulation of Stochastic Differential Equations.* SIAM Review, 43(3), 525–546.

- Kloeden, P.E. & Platen, E. (1992). *Numerical Solution of Stochastic Differential Equations.* Springer.

- Øksendal, B. (2003). *Stochastic Differential Equations: An Introduction with Applications.* Springer.

**Author:** Nguyen Thai Ngan  
**Affiliation:** University of Economics and Law (UEL)  
**Field:** Mathematical Economics / Computational Mathematics  
**Language:** Python 
