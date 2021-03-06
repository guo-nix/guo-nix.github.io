---
title: 7. 整数反转
author: guo-nix
date: 2022-07-06 15:30:00 +0800
categories: [LeetCode, 数学]
tags: [数据结构与算法]  
math: true
---

## 问题描述


给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。 


```
输入: 123
输出: 321
```
{: file="示例 1"}


```
输入: -123
输出: -321
```
{: file="示例 2"}


```
输入: 120
输出: 21
```
{: file="示例 3"}

> 注意: 假设当前环境只能存储得下 32 位的有符号整数，则其数值范围为 `[−2^31, 2^31 − 1]`。
> 请根据这个假设，如果反转后整数溢出那么就返回 0。 

Related Topics 数学 



### 方法一、数学

记 rev 为翻转后的数字，为完成翻转，可以重复「弹出」x 的末尾数字，将其「推入」rev 的末尾，直至 x 为 0。

要在没有辅助栈或数组的帮助下「弹出」和「推入」数字，可以使用如下数学方法：


```
// 弹出 x 的末尾数字 digit
digit = x % 10
x /= 10

// 将数字 digit 推入 rev 末尾
rev = rev * 10 + digit
```

题目需要判断反转后的数字是否超过 32 位有符号整数的范围 `[-2^{31},2^{31}-1]`，例如 `x=2123456789` 反转后的 `rev=9876543212>2^31−1=2147483647`，超过了 32 位有符号整数的范围。

因此需要在「推入」数字**之前**，判断是否满足 `−2^31≤rev⋅10+digit≤2^31−1`。若该不等式不成立则返回 0。

但是题目要求不允许使用 64 位整数，即运算过程中的数字必须在 32 位有符号整数的范围内，因此不能直接按照上述式子计算，需要另寻他路。


![](/assets/img/leetcode/1-50/7_fig1.png)


```java
class Solution {
    public int reverse(int x) {
        int rev = 0, digit = 0;
        while(x!=0){
            if(rev < Integer.MIN_VALUE/10 || rev > Integer.MAX_VALUE/10){
                return 0;
            }
            digit = x % 10;
            x /= 10;
            rev = rev * 10 + digit;
        }
        return rev;
    }
}
```

复杂度分析

- 时间复杂度：`O(log∣x∣)`。翻转的次数即 x 十进制的位数。

- 空间复杂度：`O(1)`。













