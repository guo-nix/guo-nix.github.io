---
title: 39. 组合总和
author: guo-nix
date: 2022-07-07 08:59:00 +0800
categories: [LeetCode, 数组]
tags: [数据结构与算法]  
math: true
---

## 问题描述


给定一个无重复元素的正整数数组 candidates 和一个正整数 target ，找出 candidates 中所有可以使数字和为目标数 target 的唯一组合。

candidates 中的数字可以无限制重复被选取。如果至少一个所选数字数量不同，则两种组合是唯一的。 

对于给定的输入，保证和为 target 的唯一组合数少于 150 个。

```
示例 1：
输入: candidates = [2,3,6,7], target = 7
输出: [[7],[2,2,3]]

示例 2：
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]

示例 3：
输入: candidates = [2], target = 1
输出: []

示例 4：
输入: candidates = [1], target = 1
输出: [[1]]

示例 5：
输入: candidates = [1], target = 2
输出: [[1,1]]
```





### 搜索回溯

对于这类寻找所有可行解的题，都可以尝试用「搜索回溯」的方法来解决。


#### 实现一



```java
class Solution {
    int n;
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        n = candidates.length;
        List<List<Integer>> ans = new ArrayList<>();
        if(n==0) return ans;
        dfs(ans, new ArrayList<>(), candidates, target, 0);
        return ans;
    }

    public void dfs(List<List<Integer>> ans, List<Integer> list, int[] candidates, int target, int index){
        if(target<0){
            return;
        }
        if(target==0){
            ans.add(new ArrayList<>(list));
            return;
        }

        for(int i=index; i<n; i++){
            list.add(candidates[i]);
            dfs(ans, list, candidates, target-candidates[i],i);
            list.remove(list.size()-1);
        }

    }
}
```


#### 实现二

定义递归函数 dfs(target, combine, idx) 表示当前在 candidates 数组的第 idx 位，还剩 target 要组合，已经组合的列表为 combine。递归的终止条件为 target <= 0 或者 candidates 数组被全部用完。那么在当前的函数中，每次可以选择跳过不用第 idx 个数，即执行 dfs(target, combine, idx + 1)。也可以选择使用第 idx 个数，即执行 dfs(target - candidates[idx], combine, idx)，注意到每个数字可以被无限制重复选取，因此搜索的下标仍为 idx。

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        List<Integer> combine = new ArrayList<Integer>();
        dfs(candidates, target, ans, combine, 0);
        return ans;
    }

    public void dfs(int[] candidates, int target, List<List<Integer>> ans, List<Integer> combine, int idx) {
        if (idx == candidates.length) {
            return;
        }
        if (target == 0) {
            ans.add(new ArrayList<Integer>(combine));
            return;
        }
        // 直接跳过
        dfs(candidates, target, ans, combine, idx + 1);
        // 选择当前数
        if (target - candidates[idx] >= 0) {
            combine.add(candidates[idx]);
            dfs(candidates, target - candidates[idx], ans, combine, idx);
            combine.remove(combine.size() - 1);
        }
    }
}
```