---
title: Operator Precedence in C
date: 2023-10-26 01:11:36
excerpt: Bug of operator precedence in C
tags: [Learning Log, C, operator, debug]
categories:
  - Daily Logs
  - Learning Logs
  - C
---

This blog record how I debugged a problem while I was implementing float subtraction solely based on integer subtraction.

# Operator Precedence in C

What is some potential problem of the following code:

This code is a special case when subtracting two float numbers with different sign.

```c
if (sign_f != sign_g) {
    sign_res = sign_f;
    mantissa_res = mantissa_f + mantissa_g;
    if (mantissa_res & BIT24_MASK == BIT24_MASK) {
      mantissa_res >>= 1;
      exp_res++;
    }
}
```

As I mentioned in the title, I was trying to check whether `mantissa_f + mantissa_g` exceeded the 2. If so, I need to reduce it to 1 point something and change the exponent. Yet the if statement does not work as I expected. This is because the **`==`** operator has a higher precedence than the **`&`** operator.

Thus, the correct way of writing is `if ((mantissa_res & BIT24_MASK) == BIT24_MASK)` or `if (mantissa_res & BIT24_MASK)`
