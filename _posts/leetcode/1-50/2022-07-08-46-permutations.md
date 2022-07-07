---
title: 10. 正则表达式匹配
author: guo-nix
date: 2022-07-08 08:30:00 +0800
categories: [LeetCode, 字符串]
tags: [数据结构与算法]  
math: true
---

## 问题描述
## 46. 全排列

给定一个不含重复数字的数组 nums ，返回其所有可能的全排列 。你可以 按任意顺序返回答案。提示：nums 中的所有整数 互不相同。


```
示例 1：
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

示例 2：
输入：nums = [0,1]
输出：[[0,1],[1,0]]

示例 3：
输入：nums = [1]
输出：[[1]]
```



### 回溯


```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        boolean[] flag = new boolean[nums.length];
        dfs(nums, ans, new ArrayList<>(), nums.length, flag);
        return ans;
    }

    public void dfs(int[] nums, List<List<Integer>> ans, List<Integer> list, int len, boolean[] flag){
        if(len==list.size()){
            ans.add(new ArrayList<>(list));
            return;
        }

        for(int i=0; i<len; i++){
            if(flag[i]==false){
                flag[i] = true;
                list.add(nums[i]);
                dfs(nums, ans, list, len, flag);
                list.remove(list.size()-1);
                flag[i] = false;
            }
        }
    }
}
```


