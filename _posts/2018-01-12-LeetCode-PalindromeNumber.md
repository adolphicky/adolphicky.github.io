---
layout: post
title: "[LeetCode]Palindrome Number"
date: 2018-01-10 
description: "回文数判定"
tag: LeetCode 
---   
## Problem Description
#### [Palindrome Number](https://leetcode.com/problems/palindrome-number/description/)
Determine whether an integer is a palindrome. Do this without extra space.

### Some hints:
```
Could negative integers be palindromes? (ie, -1)

If you are thinking of converting the integer to string, note the restriction of using extra space.

You could also try reversing an integer. However, if you have solved the problem "Reverse Integer", you know that the reversed integer might overflow. How would you handle such case?

There is a more generic way of solving this problem.
```

## Solution
```python
class Solution(object):
    def isPalindrome(self, x):
        """
        :type x: int
        :rtype: bool
        """
        res = 0
        des = x
        if des < 0:
            return False
        while des > 0:
            res = res*10+des%10
            des = des//10
        return res == x
```

解题伊始被第一行提示唬住了，所以老老实实的做了数字倒序。

忍不住还是试了下提交利用python的分片倒序，结果也是能AC！！！（哪哪都是套路取胜。。。）

```
class Solution(object):
    def isPalindrome(self, x):
        """
        :type x: int
        :rtype: bool
        """
        return False if x < 0 else str(x)[::-1] == str(x)
```