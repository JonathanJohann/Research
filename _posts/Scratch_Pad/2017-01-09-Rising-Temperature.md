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

So what I tried to do above was join the same table on itself but on a shifted key. Unfortunately, this did not work out as planned. In the best case, I only made it through 7 out of the 14 test cases. I'm only slightly consoled by the fact that the acceptance was as low as 26.4%.

**Optimal Solution:**

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_rising_temp.png)

The result ended up calling two tables with no join statement and using DATEDIFF to solve the problem followed by an AND statement which checks to see if the temperature is higher.
