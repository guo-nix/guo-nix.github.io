---
title: 27. 移除元素
author: guo-nix
date: 2022-07-07 08:47:00 +0800
categories: [LeetCode, 数组]
tags: [数据结构与算法]  
math: true
---

## 问题描述



给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

提示：

- `0 <= nums.length <= 100`
- `0 <= nums[i] <= 50`
- `0 <= val <= 100`

### 方法一、双指针

由于题目要求删除数组中等于 val 的元素，因此输出数组的长度一定小于等于输入数组的长度，可以把输出的数组直接写在输入数组上。可以使用双指针：右指针 right 指向当前将要处理的元素，左指针 left 指向下一个将要赋值的位置。

如果右指针指向的元素不等于 val，它一定是输出数组的一个元素，就将右指针指向的元素复制到左指针位置，然后将左右指针同时右移；

如果右指针指向的元素等于val，它不能在输出数组里，此时左指针不动，右指针右移一位。

整个过程保持不变的性质是：区间 [0,left) 中的元素都不等于 val。当左右指针遍历完输入数组以后，left 的值就是输出数组的长度。

这样的算法在最坏情况下（输入数组中没有元素等于 val），左右指针各遍历了数组一次。


```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int n = nums.length;
        int left = 0;
        for(int right=0; right<n; right++){
            if(nums[right]!=val){
                nums[left] = nums[right];
                left++;
            }
        }
        return left;
    }
}
```

复杂度分析

- 时间复杂度：O(n)，其中 n 为序列的长度。只需要遍历该序列至多两次。

- 空间复杂度：O(1)。只需要常数的空间保存若干变量。


### 方法二、双指针优化

依然使用双指针，两个指针初始时分别位于数组的首尾，向中间移动遍历该序列。


如果左指针 left 指向的元素等于 val，此时将右指针 right 指向的元素复制到左指针 left 的位置，然后右指针 right 左移一位。如果赋值过来的元素恰好也等于 val，可以继续把右指针 right 指向的元素的值赋值过来（左指针 left 指向的等于 val 的元素的位置继续被覆盖），直到左指针指向的元素的值不等于 val 为止。

当左指针 left 和右指针 right 重合的时候，左右指针遍历完数组中所有的元素。

这样的方法两个指针在最坏的情况下合起来只遍历了数组一次。与方法一不同的是，方法二避免了需要保留的元素的重复赋值操作。


```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int left = 0;
        int right = nums.length-1;
        while (left <= right) { //必须要有等号
            if (nums[left] == val) {
                nums[left] = nums[right];
                right--;
            } else {
                left++;
            }
        }
        return left;
    }
}
```


复杂度分析

- 时间复杂度：O(n)，其中 n 为序列的长度。只需要遍历该序列至多一次。

- 空间复杂度：O(1)。只需要常数的空间保存若干变量。








