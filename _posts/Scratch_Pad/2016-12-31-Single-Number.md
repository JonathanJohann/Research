---
layout: post
hidden_category: sp
title: LeetCodeOJ - Single Number
weight: 10
hard_wrap: false
---

## LeetCodeOJ - Single Number

A colleague mentioned LeetCode OJ was a good resource for getting better at coding and, in general, creating better solutions to coding problems. As a result, I'll try to tackle some of these problems from now on.

So the below is for [question 136: Single Number](https://leetcode.com/problems/single-number/).

The objective is, given an array of integers, every element appears twice except for one. Find that single one.

So the difficulty in this problem is the run time complexity and not using extra memory.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/single_number.png)


But, unfortunately, it wasn't good enough. I couldn't think of a fast enough way to implement this. However, I had forgotten about bitwise maniupulation (which ended up being the key).

The optimal solution was:


![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_single_number.png)

So you can see above that the optimal solution is to XOR all of the values. Why? Because the following:
![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/explanation_single_number.png)

All of the repeated values will be XOR'ed and a value of 0 will be returned. However, the remaining N value will be left with 0 and return a value of N!
