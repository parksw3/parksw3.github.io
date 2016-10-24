---
layout: post
title: "Competition model"
---

For Math 3MB3, introduction to modelling course, my group has to study [three-competitors model](http://epubs.siam.org/doi/abs/10.1137/0129022), and I really didn't feel like studying for midterms so I decided to play around with the model a bit. I was especially interested in the cyclic behaviour of the model so I wanted to see if it could be found in $n$-competitor model as well, where $n > 3$. Naturally, the first thing I did was to work with the simplest case, $n = 4$:

$$
\begin{aligned}
\frac{dN_1}{dt} &= N_1 (1 - N_1 - \alpha N_2 - \beta N_3 - \gamma N_4), \\
\frac{dN_2}{dt} &= N_2 (1 - \gamma N_1 - N_2 - \alpha N_3 - \beta N_4), \\
\frac{dN_3}{dt} &= N_3 (1 - \beta N_1 - \gamma N_2 - N_3 - \alpha N_4), \\
\frac{dN_4}{dt} &= N_4 (1 - \alpha N_1 - \beta N_2 - \gamma N_3 - N_4)
\end{aligned}
$$

While trying out different parameters, I noticed that this system exhibits a cyclic behaviour for parameters such that $\alpha + \gamma = 2$ and $\beta = 1$ but switching $\beta$ with $\alpha$ (or with $\gamma$) such that $\beta + \gamma = 2$ and $\alpha = 1$ would not work. Looking at the equations, it should have been obvious that I can't change the parameters around like that, but instead I went on to figure out what was going on following May and Leonard's approach.

First, linearization of the model at the four-species equilibrium $(1, 1, 1, 1)/(1 + \alpha + \beta + \gamma)$ yields the following system:

$$
\frac{d}{dt} \begin{pmatrix}
N_1\\
N_2\\
N_3\\
N_4
\end{pmatrix} = -\begin{pmatrix}
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


Define

$$
\begin{aligned}
N_T &\equiv N_1 + N_2 + N_3 + N_4\\
P_T &\equiv N_1 N_2 N_3 N_4
\end{aligned}
$$

When $\alpha + \gamma = 2$, we get

$$
\begin{aligned}
dN_T/dt &= N_T - N_T^2\\
d[\ln P_T]/dt &= 4 d[\ln N_T]/dt
\end{aligned}
$$

...

It seems like $N_1$ and $N_3$ (or $N_2$ and $N_4$) have some relationship but I can't figure it out..

$$
\begin{aligned}
d[\ln N_1 N_3]/dt &= 2 - 2(N_T)\\
d[\ln N_1 N_3]/dt &= 2 d[\ln N_T]/dt\\
\frac{N_1(t) N_3(t)}{N_1(0) N_3(0)} &= \left(\frac{N_T(t)}{N_0(t)}\right)^2 \\
\end{aligned}
$$

This shows that $N_1$ and $N_3$ lie on hyperbola (so does $N_2, N_4$ pair).
