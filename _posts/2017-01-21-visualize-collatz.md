---
layout: post
title: "Visualizing the Collatz sequence using WolframR"
---

About a month ago, I came across an interesting way of visualizing the [Collatz sequence](https://en.wikipedia.org/wiki/Collatz_conjecture) on [reddit](https://www.reddit.com/r/math/comments/38cg9r/a_beautiful_picture_related_to_collatz_conjecture/). Since the original code is written in Mathematica, I thought it would be fun to implement it in R and play around with it.

Implementation wasn't very difficult, and I was able to produce some nice figures. However, there were couple things that still bugged me: (1) the code didn't seem general enough to be applied to other situataions, and (2) I wasn't sure how I would present the code to other people. 

The first part wasn't a big problem. I could just sit down for couple more hours and fix the code. The second part was a bit more complicated. I wanted to post it on my blog first but going through the code line by line and explaining it didn't seem interesting at all. I could post the pictures in the [aRt gallery](https://parksw3.github.io/gallery/) but I wanted to talk about it more. I also wanted to let other people try the code without making them go through the entire repository. So I decided to write an R package.

`wolframR` is basically an attempt to implement some of the Wolfram functions in R. So far, I've only written three functions and they're not very useful in terms of statistics. However, I think they're still interesting, and I want to talk about them.

First, to install the package, you have to [download R](https://cran.r-project.org/mirrors.html). If you don't have `devtools` package installed already, you can install it like this: `install.packages("devtools")`. Finally, you can install `wolframR` from my github repository:

```r
devtools::install_github("parksw3/wolframR")
```

Once you've downloaded the package, you can load it via `library(wolframR)`, and you can explore the package!

In this post, I'm going to try to explain each function briefly. If you're interested, you can read more about it via `?` command (for example, `?nest_while` opens a description of the `nest_while` function). Wolfram documentations also contain many examples that I didn't include the description. If you want to look at the implementation itself, you can visit the [repository](https://github.com/parksw3/wolframR).

nest
====

`nest` allows a function to be applied recursively $n$ times. It can either return a result of the computation or return a function that will compute the function recursively. For example, if you want to apply the function $x^2 + 1$ recursively, you can do

```r
nest(2, x^2 + 1, 3) 
```
or

```r
f <- function(x) x^2 + 1
nest(2, f, 3)
```

Both code will compute 

$$
f(x) = \left(\left(x^2 + 1\right)^2 + 1\right)^2 + 1
$$

and return the result. Note that `nest` can take an expression as well as a function as an argument.

Now, what do I mean by 'a function that will compute the function recursively'? Suppose you want to apply [multi-log transformation](http://onlinelibrary.wiley.com/doi/10.2307/20168092/pdf) on your data. Writing a multi-log transformation function in R can look ugly:

```r
D <- function(x) log(1 + log(1 + log(1 + log(1 +x))))
```

`nest` solves this problem easily:

```r
D <- nest(x, log(1 + x), 4)
```

More examples can be found in the [Wolfram documentation](http://reference.wolfram.com/language/ref/Nest.html).

nest_while
====

`nest_while` is very similar to `nest`. It applies the function recursively until the condition is satisfied. For example, suppose we want to find a root of the following function:

$$
f(x) = x^5 - 4 x^3 + 159
$$

Using `nest_while`, we can easily implement the [Secant method](https://en.wikipedia.org/wiki/Secant_method), one of many root finding algorithms:

```r
f <- function(x) x^5 - 4 * x^3 + 159
secant <- function(x) c(x[2], x[2] - f(x[2]) * diff(x)/diff(f(x)))

res <- nest_while(c(2, 3), secant, abs(f(x[2])) > 10^-5, m = "all")
```

By typing `length(res) - 1`, we can see that it took 303 iterations to find the root (I subtracted 1 because `res[[1]]` contains the initial condition). Lastly, `f(res[[304]][2])` confirms that we have the correct root. It's probably a lot more efficient to use `uniroot`, but this is fun!

Another example that I want to mention involves Happy Numbers.

<p class="message">
A Happy number is a number that if one squares its digits and add them together, and then take the result and square its digits and add them together again and keep doing this process, one comes down to the number 1.
</p>

We can find Happy numbers less than or equal to 1000 with three lines of code using `nest_while`:

```r
digitsum2 <- function(x) sum((floor(x / 10^(0:(nchar(x) - 1))) %% 10)^2)
res <- sapply(1:1000, function(i) nest_while(i, digitsum2, x != 1 & x != 4))
which(res == 1)
```
`x != 4` is included in the condition because any number that is not happy will end in a loop: 4, 16, 37, 58, 89, 145, 42, 20, 4...

Again, more examples can be found in the [Wolfram documentation](http://reference.wolfram.com/language/ref/Nest.html).

angle_path
====

`angle_path` is a fairly simple function. You specify an angle of each step, and it creates a path based on the angles. For example, you can create a [regular $n$-gon](https://en.wikipedia.org/wiki/Regular_polygon) with the following code:

```r
n <- 5
angle_path(rep(360/n, n), deg = TRUE)
```

This function is straightforward so I'm not going to explain any further here. It does have some more functionality, so if you're curious, you can try `?angle_path` and/or look at [Wolfram documentation](https://reference.wolfram.com/language/ref/AnglePath.html).

Collatz sequence
==========

Finally, to see the visualization of the Collatz sequence, you can look at the vignette:

```r
vignette("collatz", package = "wolframR")
```

So that's it. I wrote the entire package so that I can write that vignette. Hopefully, I can extend the package and find more applications.
