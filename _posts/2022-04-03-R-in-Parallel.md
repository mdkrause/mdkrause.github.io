---
title: "R Parallel for Routine Operations"
date: '2022-04-03'
permalink: /posts/2022/04/RParallel/
tags:
- R
- parallel
---

Parallel computing is advantageous when a large problem can be broken down into 
smaller pieces. A good example is when running parallel multiple-chain MCMC, 
a common task in Bayesian statistics. 

But what about when working with routine files in a breeding program? Or in any 
field of work that you need to combine/concatenate multiple files from different 
sources?

As an example, I computed 1,000 .rds files from a simulation pipeline I've built
for my Ph.D. As an example, I will load just 20 of these files. There we go:

``` r
# preparing files
dir <- '/home/mdkrause/Desktop/results/MET/'
n <- list.files(dir)[grep(".rds", list.files(dir))] ; n <- n[1:20]
out <- vector("list", length(n))

### single-threaded
system.time(
for (i in 1:length(n)){
  out[[i]] <- readRDS(paste0(n[i]))
   }
)

### multiple-threaded
library(foreach)
library(doParallel)
cl <- makeCluster(3)
registerDoParallel(cl)

system.time(
  sim <- foreach(i = n, .combine = "rbind") %dopar%{
  temp <- data.frame(readRDS(paste0(i)))}
)
stopCluster(cl)
```

LOADING...

