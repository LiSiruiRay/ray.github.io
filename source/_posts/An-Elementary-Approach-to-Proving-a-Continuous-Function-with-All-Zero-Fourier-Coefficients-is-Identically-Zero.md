---
title: >-
  An Elementary Approach to Proving a Continuous Function with All Zero Fourier
  Coefficients is Identically Zero
date: 2024-03-02 20:36:18
math: true
tags:
---

When delving into the fascinating world of Fourier series and their convergence properties for functions within the $ C^N $ space, we encounter a fundamental question: Can a continuous function, which has all its Fourier coefficients equal to zero, be anything other than identically zero? This question not only probes the nature of Fourier analysis but also tests our understanding of function representation and orthogonality. In this blog post, we explore an elementary approach to proving that a periodic $ C^1 $ function with all its Fourier coefficients set to zero is indeed identically zero.

### The Context of the Proof

The importance of this proof arises while proving the convergence of Fourier series for $ C^N $ functions. The Fourier series allows us to represent periodic functions as the sum of sines and cosines, which are elemental to various fields of physics and engineering, particularly where waveforms and oscillations come into play.

### Simplifying the Problem

To prove our theorem, it suffices to consider the case where $ h(0) > 0 $. Why? Because any point on a periodic function can be shifted to the origin through a phase shift, and if $ h(0) < 0 $, we can simply consider $-h(x)$ instead. This reduces our problem to a standard form, focusing our attention on the behavior of the function at a single, crucial point.

### Strategy for the Proof

Our general approach to proving the theorem is by contradiction. We assume that there exists a continuous periodic function $ h $ with all its Fourier coefficients equal to zero, yet $ h $ is not identically zero. To navigate this proof, we introduce a special function $ P_n(x) $, constructed to aid in our analysis.

### Crafting the Function $ P_n(x) $

The function $ P_n(x) $ is defined as:

$P_n(x) = (1 + cos(2\pi x) - cos(2\pi \delta))^n$

This function is particularly chosen for its Fourier series properties and its behavior within and outside a certain interval.

### Key Properties of $ P_n(x) $

$ P_n(x) $ possesses several critical properties that are pivotal in our proof:

1. It is composed entirely of Fourier basis functions.
2. Within the interval $|x| \leq \delta$, $P_n(x) > 1 $, ensuring that it amplifies the value of $ h(x) $ within this region.
3. Outside this interval, but within $|x| \leq \frac{1}{2}$, $ P_n(x) < 1 $, which diminishes the impact of $ h(x) $.
4. As $ n \to \infty $, $ P_n(x) $ approaches zero for $|x| > \delta$, aligning with the properties of the Dirac delta function.

<iframe scrolling="no" title="P_function_fourier_zero_proof" src="https://www.geogebra.org/material/iframe/id/dr6pm969/width/700/height/500/border/888888/sfsb/true/smb/false/stb/false/stbh/false/ai/false/asb/false/sri/false/rc/false/ld/false/sdz/true/ctl/false" width="700px" height="500px" style="border:0px;"> </iframe>

### The Contradiction

Using these properties, we integrate $ h(x)P_n(x) $ over one period and find that the integral must be zero since $ h(x) $ has zero Fourier coefficients. However, due to the properties of $ P_n(x) $, particularly within the $\delta$ interval, we are guaranteed a non-zero contribution to the integral from $ h(0) > 0 $, leading to a contradiction unless $ h $ is zero everywhere.

### Detailed Proof Steps

**Theorem**: Let $ h $ be a $ C^1 $ periodic function with period $ L $. If all the Fourier coefficients of $ h $ are zero, then $ h(x) $ is identically zero for all $ x $.

**Proof**:

1. **Assumption**: Assume $ h $ is not identically zero, but all its Fourier coefficients are zero. Without loss of generality, let us assume $h(0) = \alpha > 0$. By continuity of $ h $, there exists a $\delta > 0$ such that $h(x) \geq \frac{\alpha}{2}$ for $|x| \leq \delta$.
2. **Constructing $ P_n(x) $**: Define $ P_n(x) $ as
   $
   P_n(x) = (1 + cos(2\pi x) - cos(2\pi \delta))^n
   $.
   $ P_n(x) $ is constructed to have the following properties:
   - $P_n(x) \geq 1 $ for $|x| \leq \delta$
   - $P_n(x) < 1 $ for $\delta < |x| \leq \frac{1}{2}$ - $ P_n(x) \to 0 $ for $\delta < |x| \leq \frac{1}{2}$ as $n \to \inf$
3. **Application of $ P_n(x) $**: Multiply $ h(x) $ by $P_n(x) $ and integrate over one period from $-\frac{L}{2}$ to $\frac{L}{2}$:
   $$
   0 = \int_{-\frac{L}{2}}^{\frac{L}{2}} h(x)P_n(x) dx
   $$
   This is because the Fourier coefficients of $ h(x) $ are zero, implying that $ h(x) $ is orthogonal to all the basis functions in the Fourier series, and hence the integral of the product is zero.
4. **Splitting the Integral**: Divide the integral into two parts: one over the interval where $|x| \leq \delta$ and the other where$|x| > \delta$:
   $$
   0 = \int_{|x| \leq \delta} h(x)P_n(x) dx + \int_{\delta < |x| \leq \frac{L}{2}} h(x)P_n(x) dx
   $$
5. **Contradiction**: As $n \to \infty$, the second integral tends to zero because $ P_n(x) \to 0 $ for $\delta < |x| \leq \frac{1}{2}$. However, in the first integral, since $h(x) \geq \frac{\alpha}{2}$for $|x| \leq \delta$ and $P_n(x) \geq 1$, the integral is bounded below by $\frac{\alpha}{2}$ times the measure of the interval $|x| \leq \delta$, which is a positive number. This leads to a contradiction because the sum of a positive number and a number tending to zero cannot be zero.
6. **Conclusion**: Since assuming that $ h $ is not identically zero leads to a contradiction, we conclude that $ h(x) $ must be identically zero for all $ x $. Thus, any $ C^1 $ periodic function with all zero Fourier coefficients is identically zero.

This completes the proof. It demonstrates the power of Fourier series and the orthogonality of trigonometric functions, providing an elegant method to prove that a function with no frequency components (i.e., all zero Fourier coefficients) must be a flat, zero function.

### Summarizing the Proof

The most critical step in our proof is the introduction of $ P_n(x) $, which serves as a magnifying glass at the origin, ensuring that if $ h(x) $ is non-zero at $ x = 0 $, it will contribute to the integral despite the zero Fourier coefficients elsewhere. This clever use of $ P_n(x) $'s properties allows us to
