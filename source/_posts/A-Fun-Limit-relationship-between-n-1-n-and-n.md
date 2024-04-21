---
title: "A Fun Limit: relationship between (n!)^(1/n) and n"
date: 2023-12-18 02:27:02
math: true
excerpt: Relationship between $(n!)^{\frac{1}{n}} and n$
tags: [Math, Analysis, Real Analysis, Limit, Order of infinity]
categories:
  - Daily Logs
  - Math
  - Analysis
---

We all know that $n^n >> n! >> a^n >> n^a >> n$, but what about $(n!)^{\frac{1}{n}}$ and $n$?

# Proof

{% asset_img limit_proof.jpg Proof %}

## A lemma

The proof of

$$
\liminf _{n\rightarrow \infty }\dfrac{a_{n+1}}{a_{n}}\leq \liminf _{n\rightarrow \infty }\sqrt[n] {a_n}\leq \limsup _{n\rightarrow \infty }\sqrt[n] {a_n}\leq \limsup _{n\rightarrow \infty }\dfrac{a_{n}+1}{a_n}
$$

Can be found [here](/2023/12/18/Relationship-between-Ratio-and-Root-test/)
