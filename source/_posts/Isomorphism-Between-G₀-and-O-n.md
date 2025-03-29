---
title: Isomorphism Between G₀ and O(n)
date: 2024-10-29 21:14:59
tags: [Math, Abstract Algebra, Vector Space, Metric, Inner Product, Motion]
categories:
  - Daily Logs
  - Math
  - Algebra
math: true
---
# Exploring the Isomorphism Between G₀ and O(n)

In the realm of linear algebra and group theory, understanding the structure and relationships between different groups of transformations is fundamental. One such intriguing relationship is the isomorphism between the group $G_0$ of motions preserving distance and the origin in $\Bbb{R}^n$, and the orthogonal group $O(n)$. In this blog post, we'll delve deep into the proof that establishes this isomorphism, ensuring clarity and consistency in our notation throughout.

## Table of Contents

1. [Introduction to $O(n)$ and $G_0$](#introduction)
2. [Definitions and Equivalent Conditions for $O(n)$](#definitions)
3. [Understanding $G_0$: Motions Preserving Distance and the Origin](#g0)
4. [Establishing the Isomorphism $G_0 \cong O(n)$](#isomorphism)
5. [Lemma: Uniqueness of Vectors Under Norm Preservation](#lemma)
6. [Conclusion](#conclusion)
7. [References](#references)

<!-- <a name="introduction"></a> -->

## 1. Introduction to $O(n)$ and $G_0$

Orthogonal groups and isometry groups play a pivotal role in various branches of mathematics and physics. The orthogonal group $O(n)$ consists of all $n \times n$ real matrices that preserve the Euclidean inner product. On the other hand, $G_0$ represents the group of motions (transformations) in $\Bbb{R}^n$ that preserve distances between points and fix the origin.

Establishing an isomorphism between $G_0$ and $O(n)$ not only deepens our understanding of these groups but also bridges the gap between linear transformations and geometric motions.

<!-- <a name="definitions"></a> -->
## 2. Definitions and Equivalent Conditions for $O(n)$

Before diving into the isomorphism, let's meticulously define $O(n)$ and explore its equivalent characterizations. Understanding these equivalencies is crucial for appreciating the structure of $O(n)$ and its relation to $G_0$.

### Definition of $O(n)$

The **orthogonal group** $O(n)$ is defined as the set of all $n \times n$ real matrices $A$ that satisfy the following equivalent conditions:

1. **Preservation of the Inner Product:**
   
   $$
   \langle Av, Aw \rangle = \langle v, w \rangle \quad \text{for all } v, w \in \Bbb{R}^n
   $$
   
   UnderstandingThis means that $A$ preserves the inner product (dot product) between any two vectors in $\Bbb{R}^n$.

2. **Orthogonality Condition:**
   
   $$
   A A^\top = I
   $$
   
   Here, $A^\top$ denotes the transpose of $A$, and $I$ is the identity matrix. This condition implies that the inverse of $A$ exists and is equal to its transpose, i.e., $A^{-1} = A^\top$.

3. **Determinant Condition:**
   
   $$
   \det(A) = \pm 1
   $$
   
   This condition signifies that $O(n)$ is a subgroup of the general linear group $GL(n)$, consisting of invertible matrices.

### Equivalence of Definitions

These definitions are equivalent in the context of real orthogonal matrices:

- **Inner Product Preservation Implies Orthogonality:**
  
  If $A$ preserves the inner product, then for any vectors $v$ and $w$:
  
  $$
  \langle Av, Aw \rangle = v^\top A^\top A w = \langle v, w \rangle
  $$
  
  For this to hold for all $v, w$, it must be that $A^\top A = I$, hence $A$ is orthogonal.

- **Orthogonality Implies Determinant Condition:**
  
  Taking determinants on both sides of $A A^\top = I$:
  
  $$
  \det(A) \det(A^\top) = \det(I) \Rightarrow (\det(A))^2 = 1 \Rightarrow \det(A) = \pm 1
  $$
  
  Thus, orthogonal matrices have determinants $\pm 1$.

- **Determinant Condition with Orthogonality:**
  
  While the determinant condition alone doesn't imply orthogonality, when combined with the invertibility (from being in $GL(n)$), it aligns with the properties of orthogonal matrices.

Understanding these equivalent conditions provides a robust foundation for exploring the relationship between $O(n)$ and $G_0$.

<!-- <a name="g0"></a> -->
## 3. Understanding $G_0$: Motions Preserving Distance and the Origin

Now, let's define the group $G_0$ and comprehend its properties.

### Definition of $G_0$

The group $G_0$ consists of all motions $m: \Bbb{R}^n \to \Bbb{R}^n$ that satisfy the following conditions:

1. **Distance Preservation:**
   
   $$
   \| m(v) - m(w) \| = \| v - w \| \quad \text{for all } v, w \in \Bbb{R}^n
   $$
   
   This ensures that the transformation $m$ preserves the Euclidean distance between any two points in $\Bbb{R}^n$.

2. **Origin Preservation:**
   
   $$
   m(0) = 0
   $$
   
   This condition fixes the origin.

### Geometric Interpretation

Geometrically, $G_0$ represents all transformations that **rigidly** move points in space without altering their relative distances or shifting the origin. Such transformations include rotations and reflections, which are precisely the elements of $O(n)$.

### Initial Inclusion: $O(n) \subseteq G_0$

It's straightforward to observe that every orthogonal matrix $A \in O(n)$ induces a motion $m_A$ defined by:
$$
m_A(v) = Av
$$
Since $A$ preserves inner products and distances (as per the equivalent conditions for $O(n)$), it naturally belongs to $G_0$. Hence, we have:
$$
O(n) \subseteq G_0
$$

However, to establish an **isomorphism**, we need to show that every element in $G_0$ corresponds uniquely to an element in $O(n)$, and vice versa.

<!-- <a name="isomorphism"></a> -->
## 4. Establishing the Isomorphism $G_0 \cong O(n)$

To prove that $G_0$ is isomorphic to $O(n)$, denoted as $G_0 \cong O(n)$, we need to demonstrate a **bijective homomorphism** between these two groups. Essentially, every motion in $G_0$ should correspond to a unique orthogonal matrix in $O(n)$, and the group operations should align accordingly.

### Step-by-Step Proof

Let's break down the proof into detailed steps, ensuring each transition is logically sound and well-explained.

#### **Step 1: Understanding the Transformation in $G_0$**

Consider a motion $m \in G_0$. By definition:
$$
\| m(v) - m(w) \| = \| v - w \| \quad \text{and} \quad m(0) = 0
$$
This implies that $m$ preserves distances and fixes the origin.

#### **Step 2: Simplifying the Transformation**

Since $m$ fixes the origin ($m(0) = 0$), we can express $m(v)$ as:
$$
m(v) = m(v) - m(0)
$$
This representation emphasizes that $m$ is a **linear transformation** because it preserves the origin and distances, properties typically associated with linear maps like rotations and reflections.

#### **Step 3: Norm Preservation**

One of the key properties of $m$ is the preservation of norms:
$$
\| m(v) \| = \| v \|
$$
This follows directly from setting $w = 0$ in the distance preservation condition:
$$
\| m(v) - m(0) \| = \| v - 0 \| \Rightarrow \| m(v) \| = \| v \|
$$
This property ensures that $m$ preserves the length of every vector in $\Bbb{R}^n$.

#### **Step 4: Handling Negative Vectors**

Using the norm preservation, we analyze the behavior of $m$ on negative vectors:
$$
\| m(-v) \| = \| -v \| = \| v \| = \| m(v) \|
$$
Additionally, since $\| m(-v) \| = \| m(v) \|$, we have:
$$
\| -m(v) \| = \| m(v) \|
$$.
Also, by that
$$
||X - Y|| = ||m(X) - m(Y)||
$$
,
We have
$$
||-m(-v) + m(v)|| = ||-(m(-v) - m(0)) + (m(v) - m(0))|| = ||m(v) - m(-v)|| = ||v - (-v)|| = 2||v||
$$.
With the above condition, we get 
$$
 ||-m(-v) + m(v)|| = 2||v|| = 2||m(v)|| = ||m(v) + m(v)||
$$
that:
$$
m(-v) = -m(v)
$$
The reasoning is rooted in the **uniqueness of vectors** under norm preservation, which we'll formalize in our lemma.

#### **Step 5: Preserving Vector Addition**

Next, we have
$$
||u + v|| = ||(u - 0) + (v - 0)|| = ||u - (-v)|| = ||m(u) - m(-v)|| 
$$
Thus
$$
= ||(m(u) - m(0)) - (m(-v) - m(0))|| = ||m(u) - m(-v)|| = ||m(u) + m(v)||
$$

Thus, we have 
$$
\| m(u + v) \| = \| u + v \|
$$
Similarly, since $m$ preserves norms of individual vectors:
$$
\| m(u) + m(v) \|^2 = \| m(u) \|^2 + 2 \langle m(u), m(v) \rangle + \| m(v) \|^2 = \| u \|^2 + 2 \langle m(u), m(v) \rangle + \| v \|^2
$$
But also:
$$
\| u + v \|^2 = \| u \|^2 + 2 \langle u, v \rangle + \| v \|^2
$$
Since $\| m(u + v) \| = \| u + v \|$ and $\| m(u) + m(v) \| = \| u + v \|$, we equate the two expressions:
$$
\| u \|^2 + 2 \langle u, v \rangle + \| v \|^2 = \| u \|^2 + 2 \langle m(u), m(v) \rangle + \| v \|^2
$$
Simplifying, we find:
$$
\langle u, v \rangle = \langle m(u), m(v) \rangle
$$
This crucial equality shows that $m$ preserves the **inner product** between any two vectors in $\Bbb{R}^n$.

#### **Step 6: Linearity**
For this part, check out [this blog](/2024/10/29/Equivalent-Definitions-of-O-n-and-Proof-of-Membership/). Or you can also watch the [video by Harvard](https://www.youtube.com/watch?v=5iNwFTZYH2k&list=PLelIK3uylPMGzHBuR3hLMHrYfMqWWsmx5&index=15&ab_channel=It%27ssoblatant)

#### **Step 7: Injectivity and Surjectivity**

To confirm that this mapping is indeed an isomorphism, we must ensure that it is both **injective** (one-to-one) and **surjective** (onto):

- **Injectivity:** Suppose $m_1, m_2 \in G_0$ such that $m_1 = m_2$. Then, their corresponding matrices $A_1, A_2 \in O(n)$ satisfy $A_1 v = A_2 v$ for all $v \in \Bbb{R}^n$, implying $A_1 = A_2$. Hence, the mapping is injective.

- **Surjectivity:** For every $A \in O(n)$, the motion $m_A(v) = Av$ belongs to $G_0$ because $A$ preserves distances and fixes the origin. Thus, every element of $O(n)$ is the image of some element in $G_0$, making the mapping surjective.

#### **Step 8: Homomorphism Property**

Finally, we verify that the mapping preserves the group operation (composition):

$$
m_1 \circ m_2(v) = m_1(m_2(v)) = A_1 (A_2 v) = (A_1 A_2) v = A_3 v = m_3(v)
$$
where $A_3 = A_1 A_2 \in O(n)$.

Since the composition of motions corresponds to the multiplication of their corresponding matrices in $O(n)$, the homomorphism property holds.

### **Conclusion of the Proof**

Combining all these steps, we've established a **bijective homomorphism** between $G_0$ and $O(n)$. Therefore, these two groups are **isomorphic**, denoted as:
$$
G_0 \cong O(n)
$$

This isomorphism reveals that the group of motions preserving distance and the origin in $\Bbb{R}^n$ is structurally identical to the orthogonal group $O(n)$, highlighting the deep interplay between linear transformations and geometric motions.

<!-- <a name="lemma"></a> -->
## 5. Lemma: Uniqueness of Vectors Under Norm Preservation

A crucial component in our proof was the assertion that:
$$
m(-v) = -m(v)
$$
To substantiate this, we rely on the following lemma:

**Lemma:** If $\| a + v \| = \| b + v \|$ for all $v \in \Bbb{R}^n$, then $a = b$.

### **Proof of the Lemma**

#### **Step 1: Expanding the Norms Using the Inner Product**

Recall that the square of the Euclidean norm of a vector $u$ is given by the inner product:
$$
\| u \|^2 = \langle u, u \rangle
$$
Thus, expanding the norms:
$$
\| a + v \|^2 = \langle a + v, a + v \rangle = \| a \|^2 + 2 \langle a, v \rangle + \| v \|^2
$$
$$
\| b + v \|^2 = \langle b + v, b + v \rangle = \| b \|^2 + 2 \langle b, v \rangle + \| v \|^2
$$

#### **Step 2: Equating the Expanded Norms**

Given that $\| a + v \| = \| b + v \|$ for all $v$, their squares are also equal:
$$
\| a \|^2 + 2 \langle a, v \rangle + \| v \|^2 = \| b \|^2 + 2 \langle b, v \rangle + \| v \|^2
$$
Subtracting $\| v \|^2$ from both sides:
$$
\| a \|^2 + 2 \langle a, v \rangle = \| b \|^2 + 2 \langle b, v \rangle
$$
Rearranging terms:
$$
2 \langle a - b, v \rangle = \| b \|^2 - \| a \|^2
$$

#### **Step 3: Analyzing for All $v \in \Bbb{R}^n$**

The equation above must hold for all vectors $v$. Observe that:

- The left-hand side, $2 \langle a - b, v \rangle$, is a **linear function** of $v$.
- The right-hand side, $\| b \|^2 - \| a \|^2$, is a **constant**.

For these two to be equal for all $v$, the only possibility is:

1. The coefficient of $v$ must be zero:
   
   $$
   a - b = 0 \Rightarrow a = b
   $$
   
2. The constant term must also be zero:
   
   $$
   \| b \|^2 - \| a \|^2 = 0
   $$
   
   Which is automatically satisfied since $a = b$.

#### **Conclusion of the Lemma**

Thus, the only solution that satisfies the equality for all $v$ is $a = b$. This confirms the lemma and solidifies our earlier assertion that $m(-v) = -m(v)$.

<!-- <a name="conclusion"></a> -->
## 6. Conclusion

Through meticulous examination and logical progression, we've established that the group $G_0$ of motions preserving distance and the origin in $\Bbb{R}^n$ is isomorphic to the orthogonal group $O(n)$. This isomorphism not only underscores the intrinsic connection between linear transformations and geometric motions but also paves the way for further explorations into the symmetry and structure of mathematical and physical systems.

Understanding such isomorphisms enhances our grasp of how different mathematical constructs relate and interact, providing a cohesive framework for various applications across disciplines.

<!-- <a name="references"></a> -->
## 7. References

1. **Math Stack Exchange:** [Why preserving norm is equivalent to preserving inner product in rigid body transformations?](https://math.stackexchange.com/questions/3028186/why-preserving-norm-is-equivalent-to-preserving-inner-product-in-rigid-body-tran)

2. **YouTube:** [Rigid Body Transformations and Orthogonal Groups](https://www.youtube.com/watch?v=5iNwFTZYH2k&list=PLelIK3uylPMGzHBuR3hLMHrYfMqWWsmx5&index=15&ab_channel=It%27ssoblatant)

These resources provide valuable insights and supplementary explanations that underpin the concepts and proofs discussed in this blog.