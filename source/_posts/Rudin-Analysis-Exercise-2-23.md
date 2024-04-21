---
title: Rudin Analysis Exercise 2.23
date: 2023-10-31 01:50:25
math: true
excerpt: Explained proof of an exercise question
tags: [Real Analysis, Rudin, Analysis, Math, Countablility]
categories:
  - Daily Logs
  - Learning Logs
  - Math
  - Analysis
---

# Rudin E2.23

This question is a prerequisite for Exercise 2.25, which corresponds to the Exercise 43.4 in _Foundations of Mathematical Analysis_

# Question

Exercise 2.23 A collection ${V_α}$ of open sets of $X$ is said to be a base for $X$ if the following is true: For every $x \in X$ and every open set $G \subset X$ such that $x \in G$ , we have $x \in V_\alpha \subset G$ for some $\alpha$. In other words, every open set in $X$ is the union of a subcollection of ${V_\alpha}$.
**Prove** that every separable metric space has a countable base. Hint: Take all neighborhoods with rational radius and center in some countable dense subset of $X$.

> A metric space is called _separable_ if it contains a countable dense subset.

## Understand:

For this question, we need to prove two things: there is a base and the base is countable (remember base is a collection of set—set of set).

We are given that $X$ is separable. This is a hint itself. $X$ will contain a countable dense subset. This provide a feature of countable.

If we see the hint, we can also connect the countability with the rational radius.

## Prove:

Since $X$ is separable, let $\{x_1, x_2, …\}$ be a dense (also countable) subset of $X$.

$\forall m \in \Bbb{N}, r\in \Bbb{Q}, r \gt 0$, we let $V_{m, r} = \{y : y \in N_{r}(x_m)\}$.

Since $\Bbb{Q}, \Bbb{N}$ are both countable, thus the collection of $V_{m, r}$ is countable.

(Notice this is not saying that $V_{m, r}$ is countable, this is saying that $\{V_{m, r}\}_{m, r}$ is countable)

Let $V = \{V_{m, r}\}$

Also, $V_{m, r}$ is open. (Actually $V_{m, r} = N_{r}(x_m)$, which is open.)

Thus we found a countable collection of open set $V$.

Now we will prove this $V$ is a base for $X$.

To achieve this, we generally want to show: $\forall \ open \ G \subset X, \exists V_{m,r} \in V: V \subset G$

Yet this is of course not enough, to we actually need to also show that for all points in $G$, there is a $V_{m, r} \in V$ such that this point also in $V_{m, r}$

Let $x \in X$, $\exists \ open \ G: x \in G$. Since $G$ is open, all points in $G$ must be interior points of $G$.

Thus $x$ is an interior point of $G$. Thus, $\exists \delta: N_\delta(x) \in G$.

Since $\{x_1, x_2, …\}$ is dense in $X$, $\exists k \in N: x_k \in N_{\frac{\delta}{2}}(x)$.

This also means that $x \in N_{\frac{\delta}{2}}(x_k)$. Thus $x \in V_{k, \frac{\delta}{2}}$. Thus, $V$ is a base of $X$.

---

The original proof I saw was from

{% asset_img original_E_2_23.png original question and proof %}

[https://minds.wisconsin.edu/bitstream/handle/1793/67009/rudin ch 2.pdf?sequence=10&isAllowed=y](https://minds.wisconsin.edu/bitstream/handle/1793/67009/rudin%20ch%202.pdf?sequence=10&isAllowed=y)

I was wondering the following questions:

- Why we need rational radius.
- Why do we always have such $x_k$ in the open ball.
- What is the second part doing.

I provided my understanding of the proof.
