---
title: 3. 无重复字符的最长子串
author: guo-nix
date: 2022-07-06 13:30:00 +0800
categories: [LeetCode, 字符串]
tags: [数据结构与算法]  
math: true
---

## 问题描述
 


给定一个字符串，请你找出其中【不含有重复字符】的最长子串的长度。 

示例 1: 

- 输入: `"abcabcbb"`

- 输出: 3 

- 解释: 因为无重复字符的最长子串是 `"abc"`，所以其长度为 3。

示例 2: 

- 输入: `"bbbbb"`

- 输出: 1

- 解释: 因为无重复字符的最长子串是 `"b"`，所以其长度为 1。

示例 3: 

- 输入: `"pwwkew"`

- 输出: 3

- 解释: 因为无重复字符的最长子串是 `"wke"`，所以其长度为 3。请注意，你的答案必须是 子串 的长度，`"pwke"` 是一个子序列，不是子串。

Related Topics 哈希表 双指针 字符串 Sliding Window 


注意：题目中并没有说只含字母，因此还可能含有数字、空格等字符。

### 方法一、滑动窗口


思路：找出从每一个字符开始的，不包含重复字符的最长子串，那么其中最长的那个字符串即为答案。如：
- 以 `(a)bcabcbb` 开始的最长字符串为 `(abc)abcbb`；
- 以 `a(b)cabcbb` 开始的最长字符串为 `a(bca)bcbb`；
- 以 `ab(c)abcbb` 开始的最长字符串为 `ab(cab)cbb`；
- 以 `abc(a)bcbb` 开始的最长字符串为 `abc(abc)bb`；
- 以 `abca(b)cbb` 开始的最长字符串为 `abca(bc)bb`；
- 以 `abcab(c)bb` 开始的最长字符串为 `abcab(cb)b`；
- 以 `abcabc(b)b` 开始的最长字符串为 `abcabc(b)b`；
- 以 `abcabcb(b)` 开始的最长字符串为 `abcabcb(b)`。

使用「滑动窗口」来解决问题：在左指针向右移动的时候，从哈希集合中移除一个字符，在右指针向右移动的时候，往哈希集合中添加一个字符。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        // 哈希集合，记录每个字符是否出现过
        Set<Character> set = new HashSet<>();
        int n = s.length();
        if(n==0) return 0;
        // 右指针
        int rk = 0, ans = 0;
        for(int i=0; i<n; i++){
            if(i!=0){
                //移除第i-1个字符
                set.remove(s.charAt(i - 1));
            }
            while(rk<n && !set.contains(s.charAt(rk))){
                set.add(s.charAt(rk));
                rk++;
            }
            ans = Math.max(ans, rk-i);
        }
        return ans;
    }
}

```


> 注意 Set 集合 contains 方法的拼写
{: .prompt-tip }