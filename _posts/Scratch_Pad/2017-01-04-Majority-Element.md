---
layout: post
hidden_category: sp
title: LeetCodeOJ - Majority Element
weight: 10
hard_wrap: false
---

## LeetCode OJ - Majority Element

So the below is for [Majority Element](https://leetcode.com/problems/majority-element/).

The objective isto find the majority element or basically the element that appears more than n/2 times in an array of size n.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/majority_element.png)

I tried to keep the code fairly concise. An after thought is that I could've provided some sort of if statement to cut off early if the n value of one of the keys reached a substantial amount or a value greater than n/2.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_majority_element.png)

Here we see that the optimal solution is also O(n) time. The primary difference it would appear is that the optimal solution used less memory. I used a dictionary whereas he/she used two integer classes.
