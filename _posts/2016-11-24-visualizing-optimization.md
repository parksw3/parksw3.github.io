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
library(tidyr)
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
    trace %<>% filter(!is.na(x1))
    
    .cut <- rowSums(trace[,1:.n] == rep(par, each = nrow(trace)))
    .start <- tail(which(.cut == .n), 1)
        
    trace %<>% slice(.start:(.start+res$fevals-1))
    
    if(is.na(res$gevals)){
        trace %<>% select(1:(.n+1))
    }
    return(trace)
}
```

* Explain the function!

Every time the objective function (or the gradient function) is called by the optimizer, `optimv` records the parameter as well as the gradient (only if it is evaluted)

What this function does is it saves all the points that the optimizer goes through as well as gradients evaluated at each point ([if it is evaluated](https://en.wikipedia.org/wiki/Derivative-free_optimization)) and returns those values at the end. Basically, it allows us to see how the optimizer gets to that point.

To run the function, we just need to define the function and set the starting parameter.

```r
f <- function(x){
    (4 - 2.1 * x[1]^2 + x[1]^4/3) * x[1]^2 +
        x[1] * x[2] + (-4 + 4 * x[2]^2) * x[2]^2
}

traceCamel <- optimv(c(-1.3, -0.3), f)
```

Let's try to visualize this. Naive approach would be to simply plot the path but that doesn't help us visualize what Nelder-Mead does. We need triangles!

```r
triangulate <- . %>%
    "["(., outer(0:2, 1:nrow(.), "+"),) %>%
    add_column(tri =  rep(1:(nrow(.)/3), each = 3)) %>%
    filter(tri <= nrow(.)/3 - 2)
```

This is what the entire optimization process looks like:

```r
## setting up the surface
surface <- emdbook::curve3d(f(c(x,y)),
    xlim = c(-2.1, 2.1),
    ylim = c(-1.1, 1.1),
    n = 301,
    sys3d = "none") %>%
    with({
        m <- matrix(z, ncol = length(x))
        dimnames(m) <- list(x, y)
        return(m)
    }) %>%
    melt %>%
    setNames(c("x1", "x2", "f"))

triCamel <- traceCamel %>%
    triangulate 

g.default <- ggplot(NULL, aes(x1, x2)) +
    geom_contour(data = surface, aes(z = f, col = ..level..), binwidth = 0.2) + 
    scale_colour_gradientn(colours = c("#F291FF", "#DFDFDF", "#91FFFF"), name = "", limits = c(-1.2, 6.1)) + 
    coord_fixed(ratio = 1) +
    theme(legend.position = "none",
        panel.grid = element_blank(),
        axis.ticks = element_blank(),
        axis.title.y = element_text(angle=0))

g.default + 
    geom_polygon(data = triCamel, aes(group = tri), fill = NA, col = "black") +
    scale_x_continuous(expand = c(0, 0), limits = c(-1.6, 0.8), name = expression(x[1])) +
    scale_y_continuous(expand = c(0, 0), limits = c(-1.1, -0.15), , name = expression(x[2]))
```

![Camel_path](/assets/2016-11-26-visualizing-optimization/camel_path.png)

It's still not very easy to see... Why not make a gif?

```r
fn <- sprintf("camel_%03d", 1:(2 * max(triCamel$tri) - 1))

df <- triCamel %>% filter(tri == 1)

for(i in 1:50){
    cat(i)
    x.scale <- (min(df$x1) + max(df$x1))/2 + 0.5 * c(-1, 1)
    y.scale <- (min(df$x2) + max(df$x2))/2 + 0.5 * c(-1, 1)
    if(min(y.scale) < -1.1) y.scale <- c(-1.1, -0.1)
    
    g.tmp <- g.default +
        geom_polygon(data = df, aes(group = tri), fill = NA, col = "black") +
        scale_x_continuous(expand = c(0,0), limits = x.scale, name = expression(x[1])) +
        scale_y_continuous(expand = c(0,0), limits = y.scale, name = expression(x[2])) +
        theme(plot.margin = unit(c(0.3, 0.3, 0.2, 0.2), "in"))
    
    ggsave(file = paste0(fn[2 * i - 1], ".png"), g.tmp, width = 8, height = 8, dpi = 300)
    
    df <- triCamel %>% filter(tri == i+1)
    
    g.tmp2 <- g.tmp + geom_polygon(data = df, fill = NA, col = "black", lty = 2)
    if(i == 19){
        x.scale <- c(-0.25, 0.75)
        g.tmp2 <- g.tmp2 + scale_x_continuous(expand = c(0,0), limits = x.scale, name = expression(x[1])) 
    }
    ggsave(file = paste0(fn[2 * i], ".png"), g.tmp2, width = 8, height = 8, dpi = 300)
}
```

![Camel_gif](/assets/2016-11-26-visualizing-optimization/camel.gif)

Work with gradient function??

```
gr <- function(x){
    c(((-4.2 * x[1] + 4 * x[1]^3/3) * x[1]^2 + 2 * (4 - 2.1 * x[1]^2 + x[1]^4/3) * x[1] + x[2]),
    (x[1] + (8 * x[2]) * x[2]^2 + 2 * (-4 + 4 * x[2]^2) * x[2]))
}
## try CG instead of BFGS?
## BFGS has a scaling problem?
data.frame(optimv(c(-1.3, -0.3), fn = f, gr = gr, method = "CG"))
```
