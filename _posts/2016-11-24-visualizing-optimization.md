---
layout: post
title: "Visualizing optimization"
tags: [R, optimization]
---

<!--excerpt.start-->Optimization is weird.<!--excerpt.end--> Recently, I've been working on a project that involves optimization, and one thing that I found especially difficult was deciding on which optimization method to use. I can just pick the *best* one or use everything with `optimx` package but there are several issues.

* 
* 
* 

So I thought it would be fun to visualize how different methods work.

Fortunately there are many test functions!

I decided to try this with [six-hump camel function](https://www.sfu.ca/~ssurjano/camel6.html):

$$
f(x_1, x_2) = \left(4 - 2.1 x_1^2 + \frac{x_1^4}{3} \right) x_1^2 + x_1 x_2 + (-4 + 4x_2^2) x_2^2
$$

![Camel](/assets/2016-11-26-visualizing-optimization/camel.png)

I was going to include the code along with the figure but it got longer than I expected so I decided to put it in a [separate file](/assets/2016-11-26-visualizing-optimization/camel.txt).

Anyways, this function has six local minimas spread around the surface, two of which are global, and it seems relatively smooth compared to some other functions.


First, let's load some packages:

```r
library(ggplot2); theme_set(theme_bw())
library(dplyr)
library(reshape2)
library(magrittr) ## for %<>%
library(tibble) ## for add_column
library(optimx)
```

Here's a wrapper function that I wrote:

```r
optimv <- function(par, fn, gr = NULL, method = "Nelder-Mead", ...){
    .size <- 200
    .n <- length(par)
    .nrow <- .size
    .ncol <- 2 * .n + 1
    .names <- paste0("x", 1:.n)
    
    trace <- matrix(nrow = .nrow, ncol = .ncol) %>%
        as_data_frame %>%
        setNames(c(.names, "f", paste0("f_", .names)))
    i <- 1
    
    objfun <- function(state){
        f <- fn(state)
        trace[i,1:(.n+1)] <<- c(state, f)
        if(i == .nrow){
            trace %<>% rbind(matrix(nrow = .size, ncol = .ncol))
            .nrow <<- .nrow + ini_size
        }
        i <<- i + 1
        return(f)
    }
    if(!is.null(gr)){
        gradfun <- function(state){
            grad <- gr(state)
            trace[(i-1),(.n+2):.ncol] <<- grad
        
            return(grad)
        }
    }else{
        gradfun <- NULL
    }
    
    res <- optimx(par = par, fn = objfun, gr = gradfun, method = method, ...)
    trace %<>% slice(1:res$fevals)
    if(is.na(res$gevals)){
        trace %<>% select(1:(.n+1))
    }
    return(trace)
}
```

* Explain the function!

To run the function, I just need to define the function and set the starting parameter.

```r
f <- function(x){
    (4 - 2.1 * x[1]^2 + x[1]^4/3) * x[1]^2 +
        x[1] * x[2] + (-4 + 4 * x[2]^2) * x[2]^2
}
traceCamel <- optimv(c(1.1, 0.5), f)
```

Let's try to visualize this. Naive approach would be to simply plot the path but that doesn't help us visualize what Nelder-Mead is doing. We need triangles!

```r
triangulate <- . %>%
    "["(., outer(0:2, 1:nrow(.), "+"),) %>%
    add_column(tri =  rep(1:(nrow(.)/3), each = 3)) %>%
    filter(tri <= nrow(.)/3 - 2)
```

This is what the entire optimization process looks like:

```r
## defining surface
surface <- emdbook::curve3d(f(c(x,y)),
    xlim = c(-2, 2),
    ylim = c(-1, 1),
    n = 81,
    sys3d = "none") %>%
    with({
        m <- matrix(z, ncol = length(x))
        dimnames(m) <- list(x, y)
        return(m)
    }) %>%
    melt %>%
    setNames(c("x1", "x2", "f"))

traceCamel %>%
    triangulate %>%
    ggplot(aes(x1, x2)) + geom_polygon(aes(group = tri, col = tri), fill = NA) +
    scale_colour_gradient(low = "#E9E9E9", high = "black")
```