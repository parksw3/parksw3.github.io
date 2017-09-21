---
layout: post
title: "Competition model"
---

For Math 3MB3, introduction to modeling course, my group has to study [three-competitors model](http://epubs.siam.org/doi/abs/10.1137/0129022), and I really didn't feel like studying for midterms so I decided to play around with the model for a bit last weekend. I was especially interested in the cyclic behaviour of the model so I wanted to see if it could be found in $n$-competitors model as well for $n > 3$. So I started working with the next simplest case, $n = 4$:

$$
\begin{aligned}
\frac{dN_1}{dt} &= N_1 (1 - N_1 - \alpha N_2 - \beta N_3 - \gamma N_4), \\
\frac{dN_2}{dt} &= N_2 (1 - \gamma N_1 - N_2 - \alpha N_3 - \beta N_4), \\
\frac{dN_3}{dt} &= N_3 (1 - \beta N_1 - \gamma N_2 - N_3 - \alpha N_4), \\
\frac{dN_4}{dt} &= N_4 (1 - \alpha N_1 - \beta N_2 - \gamma N_3 - N_4)
\end{aligned}
$$

While trying out different parameters, I noticed that this system exhibits a cyclic behaviour that is slightly different from that of three-competitors model when $\alpha + \gamma = 2$ and $\beta = 1$:

![Figure 1](/assets/2016-10-25-competition-model/dynamics.png)

What's particular about this cycle is that $N_1$ and $N_3$ seem to be paired (and so do $N_2$ and $N_4$). I tried swtiching $\beta$ with $\alpha$ (and with $\gamma$) such that $\beta + \gamma = 2$ and $\alpha = 1$ to see if I can pair $N_1$ with $N_2$ (and with $N_4$) but I couldn't get it to work. Looking at the equations, I should have realized that it can't work, but instead I went on to figure out what was going on.

Before we talk about eigenvalues, let's look at how the system behaves when $\alpha + \gamma = 2$ and $\beta = 1$. Similar to three-comeptitors model, taking the sum of all four states, $N_T \equiv N_1 + N_2 + N_3 + N_4$, yields following equation:

$$
\begin{aligned}
dN_T/dt &= N_T - N_T^2,
\end{aligned}
$$

If you have read the paper that I linked above, this result shouldn't be surprising at all. All this tells us is that $N_T$ will converge to 1:

$$
\lim\limits_{t \to \infty} N_T = 1
$$

Now, here's the interesting bit about this model: unlike three-competitors model, we can actually separate the system into $(N_1, N_3)$ pair and $(N_2, N_4)$ pair by defining

$$
\begin{cases}
P_1 \equiv N_1 N_3,\\
P_2 \equiv N_2 N_4.
\end{cases}
$$

After some algebra, we get

$$
\frac{P_1(t)}{P_1(0)} = \frac{P_2(t)}{P_2(0)} = \left(\frac{N_T(t)}{N_T(0)} \right)^2.
$$

Knowing that $N_T$ will converge to $1$, we find that pairs $(N_1, N_3)$ and $(N_2, N_4)$ will each lie on distinct hyperbolas after sufficient amount of time:

$$
\begin{cases}
N_1 N_3 = c_1 = N_1 (0) N_3 (0)/N_T(0)^2\\
N_2 N_4 = c_2 = N_2 (0) N_4 (0)/N_T(0)^2
\end{cases}
$$

Indeed, you can see that the paths of $(N_1, N_3)$ and $(N_2, N_4)$ pairs lie on hyperbola, $f(x) = c/x$, where $c$ is determined by the initial values:

![Figure 2](/assets/2016-10-25-competition-model/phase.png)

Now, we want to look at the eigenvalues. Following May and Leonard's approach, my first instinct was to linearize the model at the four-species equilibrium $(1, 1, 1, 1)/(1 + \alpha + \beta + \gamma)$: 

$$
\frac{d}{dt} \begin{pmatrix}
N_1\\
N_2\\
N_3\\
N_4
\end{pmatrix} = - \frac{1}{1 + \alpha + \beta + \gamma} \left( \begin{pmatrix}
1 & \alpha & \beta & \gamma\\
\gamma & 1 & \alpha & \beta\\
\beta & \gamma & 1 & \alpha\\
\alpha & \beta & \gamma & 1
\end{pmatrix} \begin{pmatrix}
N_1\\
N_2\\
N_3\\
N_4
\end{pmatrix} - \begin{pmatrix}
1\\
1\\
1\\
1
\end{pmatrix}\right)
$$

Since we have a [circulant matrix](https://en.wikipedia.org/wiki/Circulant_matrix), we can easily find its eigenvalues:

$$
\begin{cases}
\lambda_1 = 1 + \alpha + \beta + \gamma \\
\lambda_2 = 1 + \beta - (\alpha + \gamma) \\
\lambda_{3,4} = (1 - \beta) \pm (\alpha - \gamma) i \\
\end{cases}
$$

When $\beta = 1$, we get purely imaginary eigenvalues, which should explain the cycle. However, if we look at the first figure, it doesn't really look like the system oscillates around the four-species equilibrium. So what's going on?

I thought about it for a while but I don't have an explanation yet. So I'm going to end the post here and go think about it more. Hopefully, I can figure something out. It will also be interesting to think about these things:

* Gain insight into characteristics of an oscillation, such as amplitude and period.
* Come up with a clearer geometric interpretation of the behaviour.
* Generalize the idea to $n$-competitors model.

If you're interested, you can also find the code that were used to produce the figures [here](https://github.com/parksw3/three_competitors_model/blob/master/misc/blog.R).
