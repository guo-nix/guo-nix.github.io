---
title: 33. 搜索旋转排序数组
author: guo-nix
date: 2022-07-07 08:53:00 +0800
categories: [LeetCode, 数组]
tags: [数据结构与算法]  
math: true
---

## 问题描述




整数数组 nums 按【升序】排列，数组中的【值互不相同】 。

在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。例如， [0,1,2,4,5,6,7] 在下标 3 处经旋转后可能变为 [4,5,6,7,0,1,2] 。

给你旋转后的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。



### 二分查找

将数组从中间分开成左右两部分的时候，一定有一部分的数组是有序的。拿示例来看，从 6 这个位置分开以后数组变成了 [4, 5, 6] 和 [7, 0, 1, 2] 两个部分，其中左边 [4, 5, 6] 这个部分的数组是有序的，其他也是如此。

这启示可以在常规二分查找的时候查看当前 mid 为分割位置分割出来的两个部分 [l, mid] 和 [mid + 1, r] 哪个部分是有序的，并根据有序的那个部分确定该如何改变二分查找的上下界，因为能够根据有序的那部分判断出 target 在不在这个部分：


- 如果 [l, mid - 1] 是有序数组，且 target 的大小满足 ([nums[l],nums[mid])，则应该将搜索范围缩小至 [l, mid - 1]，否则在 [mid + 1, r] 中寻找。

- 如果 [mid, r] 是有序数组，且 target 的大小满足 (nums[mid+1],nums[r]]，则应该将搜索范围缩小至 [mid + 1, r]，否则在 [l, mid - 1] 中寻找。


> 为什么要加等号？mid 比较过了，所以绝对不可能相等，但是 start 是有可能的，即整数向下取整的时候 mid 可能与 start 相等，所以需要加。


```java
//二分查找
class Solution {
    //数组中的值 互不相同
    public int search(int[] nums, int target) {
        if(nums==null || nums.length==0) return -1;

        int i=0, j = nums.length-1;
        
        while(i<=j){
            int mid = (i+j)/2;
            if(nums[mid]==target){
                return mid;
            }else if(nums[mid]<nums[j]){
                //在右半边
                //要有等号[1,3]
                if(nums[mid]<= target && target<=nums[j]){
                    i = mid +1;
                }else{
                    j = mid -1;
                }
            }else{
                //在左半边
                //要有等号[1,3]
                if(nums[i]<=target && target<=nums[mid]){
                    j = mid -1;
                }else{
                    i = mid +1;
                }

            }

        }
        return -1;
    }
}
```


复杂度分析

- 时间复杂度： O(logn)，其中 n 为 nums 数组的大小。整个算法时间复杂度即为二分查找的时间复杂度 O(logn)。

- 空间复杂度： O(1) 。只需要常数级别的空间存放变量。



#### 错误的解法

```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums==null || nums.length==0) return -1;
        int l = 0, r = nums.length-1;
        while(l<=r){
            int mid = (l+r)/2;
            if(nums[mid]==target){
                return mid;
            }else if(nums[l]<nums[mid]){ //🍒 错误：整数向下取整，l 有可能等于 mid。
                //左边有序 （判断条件必须是nums[l]<=nums[mid]）
                //否则 [3,1] target=1 报错，返回-1
                if(nums[l]<=target && target<=nums[mid]){
                    r = mid-1;
                }else{
                    l = mid+1;
                }
            }else{
                //右边有序
                if(nums[mid]<=target && target<=nums[r]){
                    l = mid+1;
                }else{
                    r = mid-1;
                }
            }
        }
        return -1;
    }
}
```