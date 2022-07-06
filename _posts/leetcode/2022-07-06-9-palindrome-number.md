---
title: 9. 回文数
author: guo-nix
date: 2022-07-06 17:30:00 +0800
categories: [LeetCode, 数学]
tags: [数据结构与算法]  
math: true
---

## 问题描述


给你一个整数 x ，如果 x 是一个回文整数，返回 true ；否则，返回 false 。

回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

例如，121 是回文，而 123 不是。`-2^31 <= x <= 2^31 - 1`

```
示例 1：
输入：x = 121
输出：true

示例 2：
输入：x = -121
输出：false
解释：从左向右读, 为 -121 。 
从右向左读, 为 121- 。
因此它不是一个回文数。

示例 3：
输入：x = 10
输出：false
解释：从右向左读, 为 01 。
因此它不是一个回文数。
```

进阶：你能不将整数转为字符串来解决这个问题吗？

### 方法一、反转一半数字

**思路**

1. 映入脑海的第一个想法是将数字转换为字符串，并检查字符串是否为回文。但是，这需要额外的非常量空间来创建问题描述中所不允许的字符串。

2. 第二个想法是将数字本身反转，然后将反转后的数字与原始数字进行比较，如果它们是相同的，那么这个数字就是回文。但是，如果反转后的数字大于 int.MAX，将遇到整数溢出问题。

3. 按照第二个想法，为了避免数字反转可能导致的溢出问题，为什么不考虑只反转 int 数字的一半？毕竟，如果该数字是回文，其后半部分反转后应该与原始数字的前半部分相同。例如，输入 1221，可以将数字 “1221” 的后半部分从 “21” 反转为 “12”，并将其与前半部分 “12” 进行比较，因为二者相同，得知数字 1221 是回文。

**算法**

首先，应该处理一些临界情况。所有负数都不可能是回文，例如：-123 不是回文，因为 - 不等于 3。所以可以对所有负数返回 false。除了 0 以外，所有个位是 0 的数字不可能是回文，因为最高位不等于 0。所以可以对所有大于 0 且个位是 0 的数字返回 false。

现在，来考虑如何反转后半部分的数字。如何知道反转数字的位数已经达到原始数字位数的一半？

由于整个过程不断将原始数字除以 10，然后给反转后的数字乘上 10，所以，当原始数字小于或等于反转后的数字时，就意味着已经处理了一半位数的数字了。

<img src="./images/9_fig1.png">

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x<0 || (x>0 && x%10==0)) return false;
        int revertedNumber = 0;
        while(x > revertedNumber){
            revertedNumber = revertedNumber*10 + x%10;
            x /= 10;
        }
        return x==revertedNumber || x==revertedNumber/10;
    }
}
```

复杂度分析

- 时间复杂度：O(logn)，对于每次迭代，会将输入除以 10，因此时间复杂度为 O(logn)。
- 空间复杂度：O(1)。只需要常数空间存放若干变量。



### 方法二、反转所有数字

- 如果反转后的数字大于 int.MAX，将遇到整数溢出问题。

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x<0) return false;
        int rev = 0, old_v = x;
        while(x != 0){
            int digit = x % 10;
            x = x / 10;
            rev = rev*10+digit;
        }
        return rev==old_v;
    }
}
```


### 方法三、字符串-双指针

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x<0) return false;
        String s = String.valueOf(x);
        int l=0, r=s.length()-1;
        while(l<r){
            if(s.charAt(l) != s.charAt(r)){
                return false;
            }
            l++;
            r--;
        }
        return true;
    }
}
```

