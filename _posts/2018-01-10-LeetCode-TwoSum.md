---
layout: post
title: "[LeetCode]Two Sum"
date: 2018-01-10 
description: "目标解的加数序列"
tag: LeetCode 
---   
## Problem Description
#### [Two Sum](https://leetcode.com/problems/two-sum/description/)
Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

### Example:
```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

## Solution
```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        if len(nums)<=1:
            return False
        di={}
        for i in range(len(nums)):
            if nums[i] in di:
                return [di[nums[i]],i]
            else:
                di[target-nums[i]]=i
```