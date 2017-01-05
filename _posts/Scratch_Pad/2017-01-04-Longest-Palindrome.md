---
layout: post
hidden_category: sp
title: LeetCodeOJ - Longest Palindrome
weight: 10
hard_wrap: false
---

## LeetCode OJ - Longest Palindrome

So the below is for [Longest Palindrome](https://leetcode.com/problems/longest-palindrome/).

Problem: Given a string which consists of lowercase or uppercase letters, find the length of the longest palindromes that can be built with those letters.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/longest_palindrome.png)

This was fairly straight forward. I added each instance to a buffer array if it wasn't in the buffer already. If it was, I removed it and added 2 to the count. At the end, if there were any remaining values in the buff, I incremented the count by 1 since a letter could be put at the center of the palindrome.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_longest_palindrome.png)

In this case, I actually like my version better because in the optimal solution for some reason the submission has O(2N) complexity. This isn't too awful but it looks like it could be better. However, it looks like this just may be due to the fact that the optimal solution is done in C++ while my solution is done in Python.
