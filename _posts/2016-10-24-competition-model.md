---
layout: post
title: "Competition model"
---

For Math 3MB3, introduction to modelling course, my group has to study [three-competitors model](http://epubs.siam.org/doi/abs/10.1137/0129022), and I really didn't feel like studying for midterms so I decided to play around with the model a bit. I was especially interested in the cyclic behaviour of the model so I wanted to see if it could be found in $n$-competitor model as well, where $n > 3$. Naturally, the first thing I did was to work with the next simplest case, $n = 4$:

$$
\begin{aligned}
\frac{dN_1}{dt} &= N_1 (1 - N_1 - \alpha N_2 - \beta N_3 - \gamma N_4), \\
\frac{dN_2}{dt} &= N_2 (1 - \gamma N_1 - N_2 - \alpha N_3 - \beta N_4), \\
\frac{dN_3}{dt} &= N_3 (1 - \beta N_1 - \gamma N_2 - N_3 - \alpha N_4), \\
\frac{dN_4}{dt} &= N_4 (1 - \alpha N_1 - \beta N_2 - \gamma N_3 - N_4)
\end{aligned}
$$

While trying out different parameters, I noticed that this system exhibits a cyclic behaviour that is slightly different from that of three-competitors model when $\alpha + \gamma = 2$ and $\beta = 1$:

{% include image name="dynamics.png" caption="A simulation" %}

However, switching $\beta$ with $\alpha$ (or with $\gamma$) such that $\beta + \gamma = 2$ and $\alpha = 1$ would not work. Looking at the equations, it should have been obvious that I can't change the parameters around like that, but instead I went on to figure out what was going on.

Before going into eigenvalues, let's start by looking at how the system behaves when $\alpha + \gamma = 2$ and $\beta = 1$. Similar to three-comeptitors model, we look at the sum of all four states $N_T \equiv N_1 + N_2 + N_3 + N_4$:

$$
\begin{aligned}
dN_T/dt &= N_T - N_T^2,
\end{aligned}
$$

Here's the interesting bit about this model: unlike three-competitors model, we can separate the system into $(N_1, N_3)$ pair and $(N_2, N_4)$ pair by defining

$$
\begin{cases}
P_1 \equiv N_1 N_3,\\
P_2 \equiv N_2 N_4.
\end{cases}
$$

After some algebra, we get

$$
\frac{P_1(t)}{P_1(0)} = \frac{P_2(t)}{P_2(0)} = \left(\frac{N_T(t)}{N_T(0)} \right)^2,
$$

meaning that pairs $(N_1, N_3)$ and $(N_2, N_4)$ will each lie on distinct hyperbolas after sufficient amount of time:

$$
\begin{cases}
N_1 N_3 = c_1 = N_1 (0) N_3 (0)/N_T(0)^2\\
N_2 N_4 = c_2 = N_2 (0) N_4 (0)/N_T(0)^2
\end{cases}
$$







First, linearization of the model at the four-species equilibrium $(1, 1, 1, 1)/(1 + \alpha + \beta + \gamma)$ yields the following system:

$$
\frac{d}{dt} \begin{pmatrix}
N_1\\
N_2\\
N_3\\
N_4
\end{pmatrix} = - \frac{1}{1 + \alpha + \beta + \gamma} \begin{pmatrix}
1 & \alpha & \beta & \gamma\\
\gamma & 1 & \alpha & \beta\\
\beta & \gamma & 1 & \alpha\\
\alpha & \beta & \gamma & 1
\end{pmatrix} \begin{pmatrix}
N_1\\
N_2\\
N_3\\
N_4
\end{pmatrix}
$$

In order for the equilibrium to be stable, all eigenvalues of the following matrix must have positive real parts:

$$
\begin{pmatrix}
1 & \alpha & \beta & \gamma\\
\gamma & 1 & \alpha & \beta\\
\beta & \gamma & 1 & \alpha\\
\alpha & \beta & \gamma & 1
\end{pmatrix}
$$

Since this matrix is [circulant](https://en.wikipedia.org/wiki/Circulant_matrix), we find that the it has the following eigenvalues:

$$
\begin{cases}
\lambda_1 = 1 + \alpha + \beta + \gamma \\
\lambda_2 = 1 + \beta - (\alpha + \gamma) \\
\lambda_{3,4} = (1 - \beta) \pm (\alpha - \gamma) i \\
\end{cases}
$$

Now, we see that setting $\beta = 1$ gives us purely imaginary eigenvalues, causing the cycle. What does $\alpha + \gamma = 2$ do, then?
