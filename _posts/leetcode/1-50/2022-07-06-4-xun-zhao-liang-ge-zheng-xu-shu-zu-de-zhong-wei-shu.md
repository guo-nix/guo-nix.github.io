---
title: 4. 寻找两个正序数组的中位数
author: guo-nix
date: 2022-07-06 14:30:00 +0800
categories: [LeetCode, 数组]
tags: [数据结构与算法]  
math: true
---

## 问题描述
 

给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的中位数。 

进阶：你能设计一个时间复杂度为 $$O(log (m+n))$$ 的算法解决此问题吗？ 


``` 
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```
{: file="示例 1" }

 
```
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```
{: file="示例 2" }


```
输入：nums1 = [0,0], nums2 = [0,0]
输出：0.00000
```
{: file="示例 3" }


```
输入：nums1 = [], nums2 = [1]
输出：1.00000
```
{: file="示例 4" }


``` 
输入：nums1 = [2], nums2 = []
输出：2.00000
```
{: file="示例 5" }


```
nums1.length == m 
nums2.length == n 
0 <= m <= 1000 
0 <= n <= 1000 
1 <= m + n <= 2000 
-106 <= nums1[i], nums2[i] <= 106 
```
{: file="提示" }

Related Topics 数组 二分查找 分治算法 


### 方法一、合并后查找

给定两个有序数组，要求找到两个有序数组的中位数，最直观的思路有以下两种：

- 使用归并的方式，合并两个有序数组，得到一个大的有序数组。大的有序数组的中间位置的元素，即为中位数。

- 不需要合并两个有序数组，只要找到中位数的位置即可。由于两个数组的长度已知，因此中位数对应的两个数组的下标之和也是已知的。维护两个指针，初始时分别指向两个数组的下标 0 的位置，每次将指向较小值的指针后移一位（如果一个指针已经到达数组末尾，则只需要移动另一个数组的指针），直到到达中位数的位置。

假设两个有序数组的长度分别为 m 和 n，上述两种思路的复杂度如何？

- 第一种思路的时间复杂度是 `O(m+n)`，空间复杂度是 `O(m+n)`。

- 第二种思路虽然可以将空间复杂度降到 `O(1)`，但是时间复杂度仍是 `O(m+n)`。



### 方法二、二分查找

如何把时间复杂度降低到 `O(log(m+n))` 呢？如果对时间复杂度的要求有 log，通常都需要用到二分查找。


根据中位数的定义，当 `m+n` 是奇数时，中位数是两个有序数组中的第 `(m+n)/2+1` 个元素，当 `m+n` 是偶数时，中位数是两个有序数组中的第 `(m+n)/2` 个元素和第 `(m+n)/2+1` 个元素的平均值。因此，这道题可以转化成寻找两个有序数组中的第 `k` 小的数，其中 `k` 为 `(m+n)/2` 或 `(m+n)/2+1`。


假设两个有序数组分别是 `A` 和 `B`。要找到第 `k` 个元素，可以比较 `A[k/2−1]` 和 `B[k/2−1]`，其中 `/` 表示整数除法。由于 `A[k/2−1]` 和 `B[k/2−1]` 的前面分别有 `A[0..k/2−2]` 和 `B[0..k/2−2]`，即 `k/2−1` 个元素，对于 `A[k/2−1]` 和 `B[k/2−1]` 中的较小值，最多只会有 `(k/2−1)+(k/2−1)≤k−2` 个元素比它小，那么它就不能是第 `k` 小的数了。

因此可以归纳出三种情况：

- 如果 `A[k/2−1]<B[k/2−1]`，则比 `A[k/2−1]` 小的数最多只有 `A` 的前 `k/2−1` 个数和 `B` 的前 `k/2−1` 个数，即比 `A[k/2−1]` 小的数最多只有 `k−2` 个，因此 `A[k/2−1]` 不可能是第 `k` 个数，`A[0]` 到 `A[k/2−1]` 也都不可能是第 `k` 个数，可以全部排除。

- 如果 `A[k/2−1]>B[k/2−1]`，则可以排除 `B[0]` 到 `B[k/2−1]`。

- 如果 `A[k/2−1]=B[k/2−1]`，则可以归入第一种情况处理。


![](/assets/img/leetcode/1-50/4_fig1.png)


可以看到，比较 `A[k/2−1]` 和 `B[k/2−1]` 之后，可以排除 `k/2` 个不可能是第 `k` 小的数，查找范围缩小了一半。同时，将在排除后的新数组上继续进行二分查找，并且根据排除数的个数，减少 `k` 的值，这是因为排除的数都不大于第 `k` 小的数。

🍒有以下三种情况需要特殊处理：

- 如果 `A[k/2−1]` 或者 `B[k/2−1]` 越界，那么可以选取对应数组中的最后一个元素。在这种情况下，必须根据排除数的个数减少 `k` 的值，而不能直接将 `k` 减去 `k/2`。

- 如果一个数组为空，说明该数组中的所有元素都被排除，可以直接返回另一个数组中第 `k` 小的元素。

- 如果 `k=1`，只要返回两个数组首元素的最小值即可。


**明确 k：第 k 个小的元素，不是指的索引！**

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int length1 = nums1.length, length2 = nums2.length;
        int totalLength = length1 + length2;
        if(totalLength % 2 ==1 ){
            int midIndex = totalLength / 2;
            double median = getKthElement(nums1, nums2, midIndex+1);
            return median;
        }else{
            int midIndex1 = totalLength / 2 - 1, midIndex2 = totalLength / 2;
            double median = (getKthElement(nums1, nums2, midIndex1+1) + getKthElement(nums1, nums2, midIndex2+1))/2.0;
            return median;
        }
    }

    public int getKthElement(int[] nums1, int[] nums2, int k){
        int length1 = nums1.length, length2 = nums2.length;
        int index1 = 0, index2 = 0;

        while(true){
            //边界处理
            if(index1==length1) return nums2[index2+k-1];
            if(index2==length2) return nums1[index1+k-1];
            if(k==1) return Math.min(nums1[index1], nums2[index2]);

            //正常情况下
            int half = k/2;
            int newIndex1 = Math.min(index1+half, length1)-1;
            int newIndex2 = Math.min(index2+half, length2)-1;
            if(nums1[newIndex1]<=nums2[newIndex2]){
                k -= (newIndex1-index1+1);
                index1 = newIndex1 + 1;
            }else{
                k -= (newIndex2-index2+1);
                index2 = newIndex2 + 1;
            }
        }
    }
}
```


复杂度分析

- 时间复杂度：`O(log(m+n))`，其中 m 和 n 分别是数组 nums1 和 nums2 的长度。初始时有 `k=(m+n)/2` 或 `k=(m+n)/2+1`，每一轮循环可以将查找范围减少一半，因此时间复杂度是 `O(log(m+n))`。

- 空间复杂度：`O(1)`。



### 方法三、划分数组


时间复杂度更低，`O(log min(m,n)))`

[题解](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xun-zhao-liang-ge-you-xu-shu-zu-de-zhong-wei-s-114/)


```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int m = nums1.length;
        int n = nums2.length;
        int left = 0, right = m;
        // median1：前一部分的最大值
        // median2：后一部分的最小值
        int median1 = 0, median2 = 0;

        while (left <= right) {
            // 前一部分包含 nums1[0 .. i-1] 和 nums2[0 .. j-1]
            // 后一部分包含 nums1[i .. m-1] 和 nums2[j .. n-1]
            int i = (left + right) / 2;
            int j = (m + n + 1) / 2 - i;

            // nums_im1, nums_i, nums_jm1, nums_j 分别表示 nums1[i-1], nums1[i], nums2[j-1], nums2[j]
            int nums_im1 = (i == 0 ? Integer.MIN_VALUE : nums1[i - 1]);
            int nums_i = (i == m ? Integer.MAX_VALUE : nums1[i]);
            int nums_jm1 = (j == 0 ? Integer.MIN_VALUE : nums2[j - 1]);
            int nums_j = (j == n ? Integer.MAX_VALUE : nums2[j]);

            if (nums_im1 <= nums_j) {
                median1 = Math.max(nums_im1, nums_jm1);
                median2 = Math.min(nums_i, nums_j);
                left = i + 1;
            } else {
                right = i - 1;
            }
        }

        return (m + n) % 2 == 0 ? (median1 + median2) / 2.0 : median1;
    }
}
```