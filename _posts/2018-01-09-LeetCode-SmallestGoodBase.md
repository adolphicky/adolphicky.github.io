---
layout: post
title: "[LeetCode]Smallest Good Base"
date: 2018-01-09 
description: "最优进制数"
tag: LeetCode 
---   
## Problem Description
#### [Smallest Good Base](https://leetcode.com/problems/smallest-good-base/description/)
For an integer n, we call k>=2 a good base of n, if all digits of n base k are 1. Now given a string representing n, you should return the smallest good base of n in string format.

### Example 1:
```
Input: “13” 
Output: “3” 
Explanation: 13 base 3 is 111.
```

### Example 2:
```
Input: “4681” 
Output: “8” 
Explanation: 4681 base 8 is 11111.
```

### Example 3:
```
Input: “1000000000000000000” 
Output: “999999999999999999” 
Explanation: 1000000000000000000 base 999999999999999999 is 11.
```

### Note: 
The range of n is [3, 10^18]. 

The string representing n is always valid and will not have leading zeros.

## Solution
### 枚举法
```
记k的最高次幂为m，从上界 int(log(n)) 向下界 1 递减枚举m

问题转化为计算1 + k + k^2 + ... + k^m = n的正整数解

由n > k^m得： k < n ** 1/m

由n < (k + 1)^m得： k > n ** 1/m - 1，此处使用了二项式定理

因此k可能的解为：int(n ** 1/m)

最后验证1 + k + k^2 + ... + k^m 是否等于 n
```

### Python代码
```python
class Solution(object):
    def smallestGoodBase(self, n):
        """
        :type n: str
        :rtype: str
        """        
        num = int(n)
        thisLen = int(math.log(num,2)) + 1
        while thisLen > 2:
            # from equation [3], we havve
            thisBase = int(num ** (1.0/(thisLen - 1)))
            # from equation [2], we have
            if num * (thisBase - 1) == thisBase ** thisLen - 1:
                return str(thisBase)
            thisLen -= 1
        return str(num - 1)
```