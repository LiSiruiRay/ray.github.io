---
title: 'LeetCode: 4, Median of Two Sorted Arrays'
date: 2025-02-16 17:39:24
tags:
---


# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->
binary search on one of the array to find the correct position.

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
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        total = (len(nums1)) + (len(nums2))
        half = total // 2

        longer, shorter = nums1, nums2
        if len(longer) < len(shorter):
            longer, shorter = shorter, longer
        
        def get_median(l, r):
            m = (l + r) // 2
            shorter_index = m
            longer_index = half - m - 2 # since zero indexed
            shorter_left_partition_right_most = shorter[shorter_index] if shorter_index >= 0 else float("-inf")
            longer_left_partition_right_most = longer[longer_index] if longer_index >= 0 else float("-inf")
            shorter_right_partition_left_most = shorter[shorter_index + 1] if (shorter_index + 1) <= len(shorter) - 1 else float("inf")
            longer_right_partition_left_most = longer[longer_index + 1] if (longer_index + 1) <= len(longer) - 1 else float("inf")

            # base case, partition is correct
            if shorter_left_partition_right_most <= longer_right_partition_left_most and longer_left_partition_right_most <= shorter_right_partition_left_most:
                if total % 2 == 0: # even amount of element
                    left_element = max(shorter_left_partition_right_most, longer_left_partition_right_most)
                    right_element = min(longer_right_partition_left_most, shorter_right_partition_left_most)
                    return (left_element + right_element) / 2
                else: # odd number of element
                    left_element = min(longer_right_partition_left_most, shorter_right_partition_left_most)
                    return left_element
            elif shorter_left_partition_right_most <= longer_right_partition_left_most:
                # longer_left_partition_right_most > shorter_right_partition_left_most
                # longer_left_partition_right_most should be smaller -> longer_left_partition should be more left (shorter) 
                # -> shorter_left_partition should be longer -> l = m + 1
                return get_median(m + 1, r)
            else: return get_median(l, m - 1)
        return get_median(0, len(shorter) - 1)
```