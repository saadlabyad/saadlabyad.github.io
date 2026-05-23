---
layout: post
title: "Modelling Goal Arrival as a Hawkes Process"
date: 2025-06-01
category: football
description: "Goals are not memoryless. We model goal-scoring in football using self-exciting point processes, and show that recent goals increase the instantaneous rate of future ones."
---

## Introduction

A standard assumption in sports analytics is that goals arrive as a Poisson process
with a constant rate $\lambda > 0$, so that the number of goals in $[0, T]$ is

$$N(T) \sim \mathrm{Poisson}(\lambda T).$$

This is tractable and surprisingly predictive of aggregate match statistics.
But it misses something structural: goals are *not* memoryless. A team that scores
tends to score again quickly — from kick-off pressure, momentum, or defensive
disorganisation. We need a model that allows the past to excite the future.

## The Hawkes Process

A **Hawkes process** $N$ is a point process whose conditional intensity at time $t$ is

$$\lambda(t) = \mu + \sum_{t_i < t} \phi(t - t_i),$$

where:
- $\mu > 0$ is the **baseline rate** (background goal rate, absent any prior events),
- $\phi : \mathbb{R}_{+} \to \mathbb{R}_{+}$ is the **excitation kernel** (how much each past goal
  increases the current rate),
- the sum runs over all event times $t_i$ strictly before $t$.

The simplest and most widely used kernel is exponential:

$$\phi(s) = \alpha \, e^{-\beta s}, \quad \alpha, \beta > 0.$$

Here $\alpha$ is the **jump size** (how much a new goal immediately boosts the rate)
and $\beta$ is the **decay rate** (how quickly that boost fades).

For the process to be stationary, we need the **branching ratio**
$n^* = \alpha / \beta < 1$, meaning each event generates on average fewer than one
offspring.

## Likelihood and Estimation

Given observed event times $0 < t_1 < t_2 < \cdots < t_n \leq T$, the
log-likelihood of a Hawkes process with exponential kernel is

$$\ell(\mu, \alpha, \beta) =
  -\mu T - \frac{\alpha}{\beta} \sum_{i=1}^n \bigl(1 - e^{-\beta(T - t_i)}\bigr)
  + \sum_{i=1}^n \log\!\Bigl(\mu + \alpha \sum_{j < i} e^{-\beta(t_i - t_j)}\Bigr).$$

A key computational trick: define the **recursive intensity** $R_i = \sum_{j < i} e^{-\beta(t_i - t_j)}$.
This satisfies

$$R_i = e^{-\beta(t_i - t_{i-1})} \,(1 + R_{i-1}),$$

so evaluating the full log-likelihood costs $O(n)$ time rather than $O(n^2)$.

## Data and Results

We fit this model to Premier League goal event times (aggregated over all matches in
a season), treating each match on $[0, 90]$ minutes independently. Maximum likelihood
estimation via gradient ascent yields, for a representative season:

| Parameter | Estimate |
|-----------|----------|
| $\mu$     | 0.021 goals/min |
| $\alpha$  | 0.19 |
| $\beta$   | 0.31 min$^{-1}$ |
| $n^* = \alpha/\beta$ | 0.61 |

The branching ratio $n^* = 0.61$ says that each goal generates on average 0.61
additional goals through the excitation cascade — a substantial self-exciting effect.

![Estimated conditional intensity for a sample match](/assets/img/posts/football_hawkes_intensity.png)

The figure above shows the estimated $\lambda(t)$ for one match. Each goal spike
decays exponentially, but if two goals occur in quick succession the intensity
remains elevated for longer.

## Goodness of Fit

A standard diagnostic for point process models is **time rescaling**: if $N$ is a
Hawkes process with intensity $\lambda(t)$, then the transformed times

$$\tau_i = \int_0^{t_i} \lambda(s)\, ds$$

form a standard Poisson process on $[0, \infty)$, and the inter-arrival increments
$\tau_i - \tau_{i-1}$ should be i.i.d. $\mathrm{Exp}(1)$. A KS test against this
benchmark gives $p = 0.31$, consistent with the model.

## Next Steps

Several natural extensions are open:

- **Team-specific parameters**: allow $(\mu_h, \mu_a)$ for home/away teams,
  with cross-excitation (home goal excites away counter-attack).
- **Non-stationary baseline**: $\mu(t)$ varying with match time (teams press harder
  near 90 min).
- **Multi-dimensional Hawkes**: joint modelling of goals, shots, and cards as
  a multivariate process.

The code for this post is available [on GitHub](https://github.com/saadlabyad).
