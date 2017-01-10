---
layout: post
hidden_category: sp
title: LeetCodeOJ - Rising Temperature
weight: 10
hard_wrap: false
---

## LeetCodeOJ - Rising Temperature

The problem can be found [here](https://leetcode.com/problems/rising-temperature/).

**The Problem:** Given a Weather table, write a SQL query to find all dates' Ids with higher temperature compared to its previous (yesterday's) dates.

**My attempt:**

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/rising_temperature.png)

So what I tried to do above was join the same table on itself but on a shift key. Unfortunately, this did not work out as planned. 

**Optimal Solution:**

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_rising_temp.png)

The result ended up calling two tables with no join statement and using DATEDIFF to solve the problem.
