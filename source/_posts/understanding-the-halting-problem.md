---
title: understanding the halting problem
date: 2025-01-23 10:04:40
math: true
tags: [Math, Proof Theory, Computer Science, Theory of Computation]
categories:
  - Daily Logs
  - Math
---

I was reading my Professor Richard Cole's note in the halting problem and I was confused on the statement for a while. 

Understanding the following two examples will help a lot in understanding the Halting Problem.

# Class Photographer, mutation of babar's paradox
In the class Note, yet figure the graph out.

# A simpler and more abstract math model

Let $H = \{ f: ran f = \{0, 1\} \}$, such $H$ does not exist.

BWOC (By way of contradicton): Assume such $H$ exists, then let $h \in H$ s.t. $h(f) = 1 - f \ \forall f \in H$ defined pointwise, then we have that $h(h) = 1 - h$, then 
$$
h = 1 \iff h(h) = h = 0
$$
Contradiction.
