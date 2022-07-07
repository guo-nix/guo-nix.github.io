---
title: 28. 实现 strStr()
author: guo-nix
date: 2022-07-07 08:48:00 +0800
categories: [LeetCode, 字符串]
tags: [数据结构与算法]  
math: true
---

## 问题描述


实现 strStr() 函数。

给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。

**说明：**

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与 C 语言的 strstr() 以及 Java 的 indexOf() 定义相符。


**提示：**

- `1 <= haystack.length, needle.length <= 10^4`
- haystack 和 needle 仅由小写英文字符组成

```
示例 1：
输入：haystack = "hello", needle = "ll"
输出：2

示例 2：
输入：haystack = "aaaaa", needle = "bba"
输出：-1
```

### 前言

本题是经典的字符串单模匹配的模型，因此可以使用字符串匹配算法解决，常见的字符串匹配算法包括暴力匹配、Knuth-Morris-Pratt 算法、Boyer-Moore 算法、Sunday 算法等，本文将讲解 Knuth-Morris-Pratt 算法。

因为哈希方法可能出现哈希值相等但是字符串不相等的情况，而 strStr 函数要求匹配结果必定正确，因此本文不介绍哈希方法，有兴趣的读者可以自行了解滚动哈希的实现（如 Rabin-Karp 算法）。

### 方法一、暴力匹配

让字符串 needle 与字符串 haystack 的所有长度为 m 的子串均匹配一次。

为了减少不必要的匹配，每次匹配失败即立刻停止当前子串的匹配，对下一个子串继续匹配。如果当前子串匹配成功，返回当前子串的开始位置即可。如果所有子串都匹配失败，则返回 −1。

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int n = haystack.length(), m = needle.length();
        for (int i = 0; i + m <= n; i++) {
            boolean flag = true;
            for (int j = 0; j < m; j++) {
                if (haystack.charAt(i + j) != needle.charAt(j)) {
                    flag = false;
                    break;
                }
            }
            if (flag) {
                return i;
            }
        }
        return -1;
    }
}
```

复杂度分析

- 时间复杂度：O(n×m)，其中 n 是字符串 haystack 的长度，m 是字符串 needle 的长度。最坏情况下需要将字符串 needle 与字符串 haystack 的所有长度为 m 的子串均匹配一次。

- 空间复杂度：O(1)。只需要常数的空间保存若干变量。



### 方法二、Knuth-Morris-Pratt 算法


**思路及算法**

Knuth-Morris-Pratt 算法，简称 KMP 算法，由 Donald Knuth、James H. Morris 和 Vaughan Pratt 三人于 19771977 年联合发表。

Knuth-Morris-Pratt 算法的核心为前缀函数，记作 π(i)，其定义如下：对于长度为 m 的字符串 s，其前缀函数 `π(i)(0≤i<m)` 表示 s 的子串 `s[0:i]` 的最长的相等的真前缀与真后缀的长度。特别地，如果不存在符合条件的前后缀，那么 `π(i)=0`。其中真前缀与真后缀的定义为不等于自身的的前缀与后缀。

举个例子说明：字符串 aabaaab的前缀函数值依次为 0,1,0,1,2,2,3。

- π(0)=0，因为 a 没有真前缀和真后缀，根据规定为 0（可以发现对于任意字符串 π(0)=0 必定成立）；

- π(1)=1，因为 aa 最长的一对相等的真前后缀为 a，长度为 1；

- π(2)=0，因为 aab 没有对应真前缀和真后缀，根据规定为 0；

- π(3)=1，因为 aaba 最长的一对相等的真前后缀为 a，长度为 1；

- π(4)=2，因为 aabaa 最长的一对相等的真前后缀为 aa，长度为 2；

- π(5)=2，因为 aabaaa 最长的一对相等的真前后缀为 aa，长度为 2；

- π(6)=3，因为 aabaaab 最长的一对相等的真前后缀为 aab，长度为 3。

有了前缀函数，我们就可以快速地计算出模式串在主串中的每一次出现。


**如何求解前缀函数**


长度为 m 的字符串 s 的所有前缀函数的求解算法的总时间复杂度是严格 O(m) 的，且该求解算法是增量算法，即可以一边读入字符串，一边求解当前读入位的前缀函数。

为了叙述方便，接下来将说明几个前缀函数的性质：

1. `π(i)≤π(i−1)+1`
   - 依据 π(i) 定义得：`s[0:π(i)−1]=s[i−π(i)+1:i]`。
   - 将两区间的右端点同时左移，可得：`s[0:π(i)−2]=s[i−π(i)+1:i−1]`。此处是 `π(i)−1` 对应的等式。
   - 依据 π(i−1) 定义得 `π(i−1)≥π(i)−1`，即 `π(i)≤π(i−1)+1`。

2. `如果 s[i]=s[π(i−1)]，那么 π(i)=π(i−1)+1`






```java

```

复杂度分析

- 时间复杂度：O(n+m)，其中 n 是字符串 haystack 的长度，m 是字符串 needle 的长度。至多需要遍历两字符串一次。

- 空间复杂度：O(m)，其中 m 是字符串 needle 的长度。只需要保存字符串 needle 的前缀函数。

 