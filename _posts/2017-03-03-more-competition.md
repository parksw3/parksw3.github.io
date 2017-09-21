---
layout: post
title: "Notes on competition dynamics"
---

Here's a question that someone asked me a few months ago:

<p class="message">
<!--excerpt.start-->
Consider two populations competing for the same resource in the same environment.
Naively, we can model the system as follows:
$$
\frac{dy_1}{dt} = r_1 y_1 \left(1 - \frac{y_1 + y_2}{N} \right),\\
\frac{dy_2}{dt} = r_2 y_2 \left(1 - \frac{y_1 + y_2}{N} \right).
$$
<!--excerpt.end-->
Given $r_1, r_2, y_1(0), y_2(0)$, is it possible to derive an analytical expression for the ratio of the sizes of the two competing populations at equilibrium?
</p>

Although this _seems_ like a simple problem, it turns out that finding analytical expressions for the equilibrium sizes is not possible.
To attempt to find the equilibrium size of each population, we can first go through some algebraic manipulation to obtain:

$$
\frac{d \log(y_1/y_2)}{dt} = \frac{r_1 - r_2}{r_1 + r_2} \frac{d \log(y_1 y_2)}{dt}.
$$

After integrating and rearranging the equations, we get the following relation:

$$
\left( \frac{y_1(t)}{y_1(0)} \right)^{1/r_1} = \left(\frac{y_2(t)}{y_2(0)} \right)^{1/r_2}
$$

In hindsight, this is almost obvious because $d(\log(y_i(t))/r_i)/dt$ is identical across all $i$ even if we extend this model to include more than two populations.
Although we can't compute the equilibrium solution analytically, we can find it numerically by substituting $y_2^\ast = N - y_1^\ast$, where $y_i^\ast = \lim\limits_{t \to \infty} y_i(t)$, into the equation and using `uniroot` in <tt>R</tt>.

If we extend the model to include $n+1$ populations, we can find the equilibrium solution by solving the following system of nonlinear equations:

$$
\left(\frac{N - \sum_{i=2}^{n+1} y_i^\ast }{y_1(0)}\right)^{1/r_1} = \left(\frac{y_j^\ast}{y_j(0)} \right)^{1/r_j},
$$

where $j = 2, 3, \dots, n+1$. In <tt>R</tt>, we can solve this by using `nleqslv` package. Let's see if this actually works.

First, we solve the differential equation using `deSolve` with arbitrary growth rates and population sizes:

```R
library(deSolve)
n <- 10
yini <- rlnorm(n, 1, 2)
pars <- list(
    r = rlnorm(n, sdlog = 0.4),
    N = 1e5
)
times <- seq(0, 20, 0.001)
gfun <- function(t, y, pars) {
    with(as.list(pars), list(r * y * (1-sum(y)/N)))
}
res <- as.data.frame(rk4(yini, times, gfun, pars))
de.sol <- unlist(res[nrow(res),-1], use.names = FALSE)
```

Then, we solve the system of nonlinear equations using the same parameter:

```R
library(nleqslv)
rfun <- function(y, pars) {
    with(as.list(pars), ((N-sum(y))/yini[1])^(1/r[1]) - (y/yini[-1])^(1/r[-1]))
}
sol <- nleqslv(yini[-1], control=list(maxit=1e3), rfun, pars=pars)
nl.sol <- c(pars$N-sum(sol$x), sol$x)
```

Do the results match?

```R
all.equal(de.sol, nl.sol)
```
```R
## [1] TRUE
```

This works! So by solving the system of nonlinear equations, we can predict which population will be most prevalent at equiliburm. Now, things get really interesting if we relate this to virulence evolution but it also gets a lot more complicated. Hopefully, I can think about that more and write about it later...
