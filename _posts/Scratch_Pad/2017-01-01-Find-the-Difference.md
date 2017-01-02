---
layout: post
hidden_category: sp
title: LeetCodeOJ - Find the Difference
weight: 10
hard_wrap: false
---

## LeetCodeOJ - Find the Difference

So the below is for [Find the Difference](https://leetcode.com/problems/find-the-difference/).

Given two strings where t has an additional letter at a random position, find the letter that was added in t.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/find_difference.png)

This looked like a good opportunity to use XOR. As we can see, the optimal solution also includes XOR. 

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/opt_find_diff.png)

As we can see, there are quite a few ways to go about this. Starting with the first, reduce and map are used. I was never aware of either function before and so I used a bit of stackoverflow.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/map_func.png)

Basically, map runs some function across an iterable. 

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/reduce_func.png)

And reduce runs some function on the first two values, then applies the function to the result of the last function and the next input value and so on.

Therefore, the best solution using XOR achieved the same thing that I had done where I turned string characters into ordinal values and converted the ordinal value back to a string character.
