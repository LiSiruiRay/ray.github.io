---
title: 'LeetCode: 852. Peak Index in a Mountain Array'
date: 2025-02-13 14:13:55
tags:
---

# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->
Use the tendence of the middle element to check either the required index is in the left or right of the middle element.

# Approach
<!-- Describe your approach to solving the problem. -->

# Complexity
- Time complexity: $O(\log n)$
<!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity:
<!-- Add your space complexity here, e.g. $$O(n)$$ -->

# Code
```python
class Solution:
            def peakIndexInMountainArray(self, arr: List[int]) -> int:
                def binary_search(l, r):
                    if r - l <= 2: # arr length less than 3
                        max_val = max(arr[l: r + 1])
                        if r <= len(arr) - 1 and arr[r] == max_val: return r
                        if l >= 0 and arr[l] == max_val: return l
                        
                        return (l + r) // 2
                    
                    # since arr has at least 3 element, this make sure that we have at least one elemnt before and after m
                    m = (l + r) // 2 
                    if arr[m - 1] < arr[m] and arr[m] > arr[m + 1]: return m
                    if arr[m - 1] < arr[m]: return binary_search(m + 1, r)
                    if arr[m] > arr[m + 1]: return binary_search(l, m - 1)
                    
                    # never reach
                    return -1
                return binary_search(0, len(arr) - 1)
```