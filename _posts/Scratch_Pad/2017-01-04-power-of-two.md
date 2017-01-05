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

I tried to keep the code fairly concise. An after thought is that I could've provided some sort of if statement to cut off early if the n value of one of the keys reached a substantial amount or a value greater than n/2.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_power_of_two.png)

Here we see that the optimal solution is also O(n) time. The primary difference it would appear is that the optimal solution used less memory. I used a dictionary whereas he/she used two integer classes.
