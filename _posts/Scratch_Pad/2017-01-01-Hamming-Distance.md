---
layout: post
hidden_category: sp
title: LeetCodeOJ - Hamming Distance
weight: 10
hard_wrap: false
---

## LeetCodeOJ - Hamming Distance

So the below is for [Hamming Distance](https://leetcode.com/problems/hamming-distance/).

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/hamming_distance.png)

The hamming distance between two integers is defined as the number of positions at which the corresponding bits are different. Therefore, I used the XOR operator to increment the count for the number of times that the two position values for the binary numbers differed.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_hamming.png)

And despite my modestly concise answer, LeetCodeOJ competitors always have a way of shrinking things down. Never knew that x^y would give the numerical answer from a binary XOR. I thought you had to do a transformation first. Nonetheless, we can see that we XOR x and y (as I had done less concisely), turn things into binary, and finally count the times XOR returned "1".
