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

Originally I thought to use sets; however, then I wouldn't be able to incorporate more than one instance of a value. So, instead I came up with this solution. It constantly pops values off of one of the lists until there are no more values to compare. Then, if a value is found in the second list, that instance is also removed and added to the vals list which will be returned.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_intersect_two_arrays_ii.png)

I consider this a bit of cheating but the optimal solution included importing another package. However, after taking a look at Counter from the Collections package, I can see why he/she used it.


![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/collections_counter.png)

At this point, it's just a matter of finding the minimum count between the two lists and adding those to output list.
