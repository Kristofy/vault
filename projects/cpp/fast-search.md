---
title: Fast Search
created: 2024-07-05
draft: false
publish: true
tags:
  - cpp
---
# Fast Search


We aim to do the fastest search query possible for a sorted list.


## Problem

Given a sorted list of of type $T$ and length $n$ and a query $q$, we want to return the index $i$ of the first element $t_i$ that is greater than or equal to $q$.

## Additional Constraints

- The list is not only sorted but contiguous in memory.
- We do not include any pre-processing time in our measurements.


It is interesting how the aproaces perform with O 0
It is also interesting to see what happends when the benchmarks run in paralell

https://en.algorithmica.org/hpc/data-structures/binary-search/
This is the base https://www.youtube.com/watch?v=1RIPMQQRBWk

There results are not exacact, and ususaly have an overhead for the data loading