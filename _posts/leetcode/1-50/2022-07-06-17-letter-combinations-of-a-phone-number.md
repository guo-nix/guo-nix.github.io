---
title: 17. 电话号码的字母组合
author: guo-nix
date: 2022-07-06 18:37:00 +0800
categories: [LeetCode, XXX]
tags: [数据结构与算法]  
math: true
---

## 问题描述



```
给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

digits[i] 是范围 ['2', '9'] 的一个数字。
```

<img src="./images/17_fig1.png">


```
示例 1：
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]

示例 2：
输入：digits = ""
输出：[]

示例 3：
输入：digits = "2"
输出：["a","b","c"]
```


### 方法一、回溯


首先使用哈希表存储每个数字对应的所有可能的字母，然后进行回溯操作。

回溯过程中维护一个字符串，表示已有的字母排列（如果未遍历完电话号码的所有数字，则已有的字母排列是不完整的）。该字符串初始为空。每次取电话号码的一位数字，从哈希表中获得该数字对应的所有可能的字母，并将其中的一个字母插入到已有的字母排列后面，然后继续处理电话号码的后一位数字，直到处理完电话号码中的所有数字，即得到一个完整的字母排列。然后进行回退操作，遍历其余的字母排列。


```java
class Solution {
    public List<String> letterCombinations(String digits) {
        List<String> ans = new ArrayList<>();
        if(digits.length() == 0 || digits==null) return ans;

        Map<Character, String> map = new HashMap<>();
        map.put('2',"abc");
        map.put('3',"def");
        map.put('4',"ghi");
        map.put('5',"jkl");
        map.put('6',"mno");
        map.put('7',"pqrs");
        map.put('8',"tuv");
        map.put('9',"wxyz");

        dfs(map, ans, digits,  new StringBuilder(), 0);
        return ans;

    }

    public void dfs(Map<Character, String> map, List<String> ans, String digits, StringBuilder sb, int i){
        if(i>=digits.length()){
            ans.add(sb.toString()); //或 new String(sb)
            return;
        }
        char c = digits.charAt(i);
        String s = map.get(c);
        for(int k=0;k<s.length();k++){
            sb.append(s.charAt(k));
            dfs(map, ans, digits, sb, i+1);
            sb.deleteCharAt(i);  //! deleteCharAt
        }
    }
}
```


复杂度分析

- 时间复杂度：O(3^m × 4^n)，其中 m 是输入中对应 3 个字母的数字个数（包括数字 2、3、4、5、6、8），n 是输入中对应 4 个字母的数字个数（包括数字 7、9），m+n 是输入数字的总个数。当输入包含 m 个对应 3 个字母的数字和 n 个对应 4 个字母的数字时，不同的字母组合一共有 3^m × 4^n 种，需要遍历每一种字母组合。

- 空间复杂度：O(m+n)，其中 m 是输入中对应 3 个字母的数字个数，n 是输入中对应 4 个字母的数字个数，m+n 是输入数字的总个数。除了返回值以外，空间复杂度主要取决于哈希表以及回溯过程中的递归调用层数，哈希表的大小与输入无关，可以看成常数，递归调用层数最大为 m+n。

