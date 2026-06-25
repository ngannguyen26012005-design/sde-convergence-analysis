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

$$dS_t = \mu S_t \, dt + \sigma S_t \, dW_t$$

where:

- $\mu$ is the drift coefficient
- $\sigma$ is the volatility
- $W_t$ is a standard Brownian motion

Applying Itô's Lemma to $\ln(S_t)$ gives the analytical solution:

$$S_T = S_0 \exp\!\left[\left(\mu - \tfrac{1}{2}\sigma^2\right)T + \sigma W_T\right]$$

This exact solution provides a **reference solution** for evaluating numerical approximation errors.

## Numerical Schemes

### Euler–Maruyama Method

Euler–Maruyama discretises the SDE directly:

$$S_{n+1} = S_n + \mu S_n \Delta t + \sigma S_n \Delta W_n \qquad \text{(strong order 0.5)}$$


with theoretical strong convergence order:

$$
O(\Delta t^{0.5})
$$

### Milstein Method

Milstein improves Euler–Maruyama by adding an Itô correction term:

$$S_{n+1} = S_n + \mu S_n \Delta t + \sigma S_n \Delta W_n + \tfrac{1}{2}\sigma^2 S_n \!\left[(\Delta W_n)^2 - \Delta t\right] \qquad \text{(strong order 1.0)}$$

with theoretical strong convergence order:

$$
O(\Delta t)
$$

The correction term improves pathwise accuracy by incorporating information from the variation of the diffusion coefficient.

## Strong and Weak Convergence

## Strong and Weak Convergence

Numerical methods for SDEs can be evaluated using two main concepts:

### Strong Convergence

Strong convergence measures the error between numerical and exact solutions along the **same stochastic path**.
E[ |S_exact - S_num| ] = O(Δt^γ), where γ represents the strong convergence order.

In this project:

- Euler–Maruyama: γ = 0.5
- Milstein: γ = 1.0
### Weak Convergence

Weak convergence measures the difference between expected values of functions of the solution.E[g(S_exact)] - E[g(S_num)]| = O(Δt^β)

This is important in financial applications because option pricing depends on expected payoffs.

For GBM, both Euler–Maruyama and Milstein have weak convergence order 1.0.

# Experimental Design

The main challenge in measuring strong convergence is ensuring that the exact solution and numerical schemes use the **same Brownian motion realization**.

Following the framework of Higham (2001), the experiment uses a shared Brownian path across all discretization levels.

## Procedure
1. Generate one fine Brownian path with step $\delta t = T / N_{\text{fine}}$,
   where $N_{\text{fine}} = 2^{12} = 4096$
2. Compute $S_T^{\text{exact}}$ using the full terminal increment $W_T = \sum_i \Delta W_i^{\text{fine}}$
3. Simulate EM and Milstein at coarser steps $\Delta t = R \cdot \delta t$
   (for $R = 2^4, 2^5, \ldots, 2^{11}$) by **summing $R$ consecutive fine increments**
   — no new random numbers are drawn
4. Average the absolute error over $M = 10{,}000$ independent paths:
   $$\text{Strong error} = \frac{1}{M} \sum_{j=1}^{M} |S_T^{(j),\text{exact}} - S_T^{(j),\text{num}}|$$
5. Plot $\log(\text{error})$ vs $\log(\Delta t)$ and fit a regression line —
   the slope gives the empirical convergence order

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
The results confirm that Milstein provides improved pathwise accuracy due to its higher-order correction term.
The project also demonstrated how numerical SDE methods can be applied to computational finance through European option pricing.

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
