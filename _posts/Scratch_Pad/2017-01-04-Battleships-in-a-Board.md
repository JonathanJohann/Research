---
layout: post
hidden_category: sp
title: LeetCodeOJ - Battleships in a Board
weight: 10
hard_wrap: false
---

## LeetCode OJ - Battleships in a Board

So the below is for [Battleships in a Board](https://leetcode.com/problems/battleships-in-a-board/).

Problem:
Given an 2D board, count how many different battleships are in it. The battleships are represented with 'X's, empty slots are represented with '.'s. You may assume the following rules:

You receive a valid board, made of only battleships or empty slots.

Battleships can only be placed horizontally or vertically. In other words, they can only be made of the shape 1xN (1 row, N columns) or Nx1 (N rows, 1 column), where N can be of any size.


At least one horizontal or vertical cell separates between two battleships - there are no adjacent battleships.


Example:

X..X

...X

...X

In the above board there are 2 battleships.

Invalid Example:

...X

XXXX

...X

This is an invalid board that you will not receive - as battleships will always have a cell separating between them.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/count_Battleships.png)

Given that there were no transformations of the matrix, I had to go through each instance. The best I was able to achieve was )(m+n). I tried to reduce the number of instanes that I had to check in order to increment the count. So only two sides are checked. If you use just two side checks and iterate through the entire matrix, you are able to count for each battleship and not double count any. Furthermore, I succeeded in implementing the solution within the memory constraints.

![png](https://raw.githubusercontent.com/JonathanJohann/Research/master/_posts/Scratch_Pad/Pics/optimal_count_Battleships.png)


The optimal solution does about the same thing. It checks to make sure that it does not access an index outside the bounds of the matrix and then increments the count by checking the current position and two side values.
