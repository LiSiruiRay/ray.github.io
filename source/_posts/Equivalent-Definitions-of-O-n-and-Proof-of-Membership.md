---
title: Equivalent Definitions of O(n) and Proof of Membership
date: 2024-10-29 21:55:33
tags: [Math, Abstract Algebra, Vector Space, Metric, Inner Product, Motion]
categories:
  - Daily Logs
  - Math
  - Algebra
math: true
---

# Equivalent Definitions of $O(n)$ and Proof of Membership

In this post, we’ll explore some equivalent definitions for the orthogonal group, $O(n)$, and prove that any transformation preserving distances and the origin in $\Bbb{R}^n$ must be in $O(n)$. Here are three common ways to define the orthogonal group:

1. $\{ A \mid A A^\top = I \}$: Orthogonal matrices $A$ satisfy $A^{-1} = A^\top$.
2. $\{ A \mid \det(A) = \pm 1 \}$: $O(n)$ is a subgroup of the general linear group $GL(n)$, consisting of matrices with determinant $\pm 1$.
3. $\{ A \mid \langle Av, Aw \rangle = \langle v, w \rangle \}$: Orthogonal transformations preserve the inner product.

Let’s consider $G_0$, the set of transformations on $\Bbb{R}^n$ that preserve distances and fix the origin:

$$G_0 = \{ m \mid \| m(v) - m(w) \| = \| v - w \| \text{ and } m(0) = 0 \}.$$

**Goal**: We want to show that if $m \in G_0$ preserves the inner product, then $m \in O(n)$.

### Proof Outline

Since $m : \Bbb{R}^n \to \Bbb{R}^n$, we know that $m(v) \in \Bbb{R}^n$. Additionally, because $m$ preserves the inner product, the images $m(e_1), \ldots, m(e_n)$ of the standard basis vectors $\{e_i\}$ form another orthonormal basis. This suggests that $m$ can be represented as a linear transformation. 

Let $A = [m(e_1), m(e_2), \ldots, m(e_n)]$, which is the matrix formed by taking the vectors $m(e_i)$ as columns. We aim to demonstrate that $m A^{-1} = I$, which would imply that $m$ acts as the matrix $A$, and further that $A^{-1} \in O(n)$.

Let’s proceed with the proof by defining $m' = m A^{-1}$ and analyzing its properties.

### Step 1: Show that $m'$ Fixes Each Basis Vector

Since $m(e_i)$ is the $i$th column of $A$, $m' (e_i) = e_i$ for each $i$.

### Step 2: Show that $m'$ Acts as the Identity on Any Vector $v$

Consider any vector $v \in \Bbb{R}^n$. We want to show that $m'(v) = v$. Notice that

$$ \langle m'(v), e_i \rangle = \langle m'(v), m'(e_i) \rangle = \langle v, e_i \rangle = v_i. $$

Since this holds for each component $i$, it follows that the $i$-th component of $m'(v)$ matches that of $v$. Therefore, we conclude that $m'(v) = v$ for all $v$, meaning $m'$ acts as the identity transformation.

### Conclusion

Since $m' = I$, we have $m = A$, where $A$ satisfies $A A^\top = I$, making $A \in O(n)$. This completes our proof that any transformation $m$ in $G_0$ that preserves the inner product is indeed an orthogonal transformation, belonging to $O(n)$.

### Summary

We demonstrated that the preservation of distance and the origin, along with the inner product, guarantees that a transformation is orthogonal. This property is fundamental in understanding the structure of transformations in $O(n)$ and their role in preserving geometric properties in $\Bbb{R}^n$.
