---
title: 'Bayesian Analyses: Part 1'
author: M. L. DeBusk-Lane
date: '2021-01-22'
slug: []
categories: [Bayesian]
tags: [simulation, regression]
subtitle: ''
summary: 'Tidymodels Bayesian Multilevel'
authors: []
lastmod: '2021-01-22T06:57:57-05:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
output: 
  html_document:
    theme: journal
    highlight: zenburn
---

# Tidymodels Bayesian Multileve Models

This blog will focus on Tidymodels' new multilevel modeling capacity. 

Although still in somewhat of a developmental condition, this new addition to Parsnip's growing list of modeling engines is great to see. 

This supplies `stan-glmer` engines for `linear_reg()`, `logistic_reg()`, and `poisson_reg()`.

Using this week's 




```r
# Load packages
if (!require("pacman")) install.packages("pacman")
```

```
## Loading required package: pacman
```

```r
pacman::p_load(tidyverse, tidymodels, tidylog)
```

To start, we need some data!!

Lets take a brief (but not so brief) detour to simulate some data. 

Make a new simulation post... 












```r
set.seed(1234) #for reproducability
nG <- 250 #number of groups
nJ <- 18 #cluster size
W1 <- 2 #level 2 coeff
X1 <- 3 #level 1 coeff

tmp2 <- rnorm(nG) #generate 20 random numbers, m = 0, sd = 1
l2 <- rep(tmp2, each = nJ) #all units in l2 have the same value
group <- gl(nG, k = nJ) #creating cluster variable
tmp2 <- rnorm(nG) #error term for level 2
err2 <- rep(tmp2, each = nJ) #all units in l2 have the same value

l1 <- rnorm(nG * nJ) #total sample size is nG * nJ
err1 <- rnorm(nG * nJ) #level 1 

#putting it all together
y <- W1 * l2 + X1 * l1 + err2 + err1
dat <- data.frame(y, group, l2, err2,l1, err1)
```

Given that we now have our data, let's take a peak at the coefficients--just for clarity. 



