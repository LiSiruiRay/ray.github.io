---
title: Leetcode 1064. Fixed Point
date: 2025-02-11 09:20:12
math: true
tags:
---

# Intuition
Binary search on the array

# Approach
If current index is less than arr[index], search for the left part, else search on the right part. If we find one, check if there are smaller ons by searching the left part, if not, then return.

# Complexity
- Time complexity: $O(\log n)$
- Space complexity: $O(1)$

Code
```python
class Solution:
    def fixedPoint(self, arr: List[int]) -> int:
        def binary_search(l, r):
            if l >= r: 
                return l if arr[l] == l else -1
            m = (l + r) // 2
            # print(l, r, m)
            if arr[m] == m and l != m and binary_search(l, m) == -1: 
                return m
            elif arr[m] == m: return binary_search(l, m)
            elif arr[m] > m:
                return binary_search(l, m - 1)
            else:
                return binary_search(m + 1, r)
                # l = m
            # if l > r: return -1
            # if l == r: return l
            # print(l, r)
            # m = (l + r) // 2
            # if arr[m] > m:
            #     return binary_search(l, m)
            #     r = m + 1
            # else:
            #     return binary_search(m, r)
            #     l = m
            
        return binary_search(0, len(arr) - 1)
        # res = binary_search(0, len(arr) - 1)
        # res = res if res == arr[res] else -1
        # return res
```