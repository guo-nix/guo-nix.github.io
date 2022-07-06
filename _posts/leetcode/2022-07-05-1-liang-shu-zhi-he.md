---
title: 1. 两数之和
author: guo-nix
date: 2022-07-05 11:33:00 +0800
categories: [LeetCode, 数组]
tags: [数据结构与算法]  
math: true
---

## 问题描述


给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。 
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。 

示例: 
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

Related Topics 数组 哈希表 



### 方法一、哈希表

算法：使用哈希表，可以将寻找 target - x 的时间复杂度降低到从 O(N) 降低到 O(1)。创建一个哈希表，对于每一个 x，首先查询哈希表中是否存在 target - x，存在则返回结果。不存在则将 x 插入到哈希表中，即可保证不会让 x 和自己匹配。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int len =  nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        for(int i=0; i<len; i++){
            if(map.containsKey(target-nums[i])){
                return new int[]{map.get(target-nums[i]),i};
            }
            map.put(nums[i],i);
        }
        return new int[0]; //或return new int[]{};
    }
}
```

**复杂度分析**

* 时间复杂度：O(N)，其中 N 是数组中的元素数量。对于每一个元素 x，可以 O(1) 地寻找 target - x。

* 空间复杂度：O(N)，其中 N 是数组中的元素数量。主要为哈希表的开销。



### 方法二、暴力枚举【不推荐】


思路：枚举数组中的每一个数 x，寻找数组中是否存在 target - x。元素不能被重复使用，只需要在 x 后面的元素中寻找 target - x。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int len =  nums.length;
        for(int i=0; i<len; i++){
            for(int j=i+1; j<len; j++){
                if(nums[i]+nums[j]==target)
                    return new int[]{i,j};
            }
        }
        return new int[0];
    }
}
```

**复杂度分析**

* 时间复杂度：O(N^2)，其中 N 是数组中的元素数量。最坏情况下数组中任意两个数都要被匹配一次。
* 空间复杂度：O(1)。
