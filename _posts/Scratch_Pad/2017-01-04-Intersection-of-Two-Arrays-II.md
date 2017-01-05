---
layout: post
hidden_category: sp
title: LeetCodeOJ - Intersection of Two Arrays II
weight: 10
hard_wrap: false
---

## LeetCode OJ - Intersection of Two Arrays II


So the below is for [Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/).

Problem:
Given two arrays, write a function to compute their intersection.

Example:
Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2, 2].

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/intersect_two_arrays_ii.png)

This was fairly straight forward. I added each instance to a buffer array if it wasn't in the buffer already. If it was, I removed it and added 2 to the count. At the end, if there were any remaining values in the buff, I incremented the count by 1 since a letter could be put at the center of the palindrome.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_intersect_two_arrays_ii.png)

In this case, I actually like my version better because in the optimal solution for some reason the submission has O(2N) complexity. This isn't too awful but it looks like it could be better. However, it looks like this just may be due to the fact that the optimal solution is done in C++ while my solution is done in Python.
