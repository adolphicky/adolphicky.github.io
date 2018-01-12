---
layout: post
title: "[LeetCode]Reverse Integer"
date: 2018-01-09 
description: "整数倒序"
tag: LeetCode 
---   
## Problem Description
#### [Reverse Integer](https://leetcode.com/problems/reverse-integer/description/)
Given a 32-bit signed integer, reverse digits of an integer.

### Example 1:
```
Input: 123
Output:  321
```

### Example 2:
```
Input: -123
Output: -321
```

### Example 3:
```
Input: 120
Output: 21
```

### Note: 
Assume we are dealing with an environment which could only hold integers within the 32-bit signed integer range. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

## Solution
```python
class Solution(object):
    def reverse(self, x):
        """
        :type x: int
        :rtype: int
        """
        if x>0:
            result = int(str(x)[::-1])
            if result <=2147483647:
                return result
            else:
                return 0
        elif x<0:
            result = int(str(-x)[::-1])
            if -result >=-2147483647:
                return -result
            else:
                return 0
        else:
            return x
```