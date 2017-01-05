---
layout: post
hidden_category: sp
title: LeetCodeOJ - Power of Two
weight: 10
hard_wrap: false
---

## LeetCode OJ - Power of Two

So the below is for [Power of Two](https://leetcode.com/problems/power-of-two/).

Problem:
Given an integer, write a function to determine if it is a power of two.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/power_of_two.png)

So here we have a pretty simple piece of code. I turned the value into binary and counted the number of 1's. If there is only one "1" value in the binary representation, then the number is a power of two.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_power_of_two.png)

Something slightly similar is done with the optimal solution. The same idea here with only one "1" value. However, the value is AND'ed with n-1. If all values are 0's, a 1 will be returned. This would indicate that there was only one "1" value in the binary representation of n and therefore it would be a power of two.
