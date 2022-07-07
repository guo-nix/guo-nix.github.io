---
title: 32. 最长有效括号
author: guo-nix
date: 2022-07-07 08:52:00 +0800
categories: [LeetCode, XXX]
tags: [数据结构与算法]  
math: true
---

## 问题描述


给你一个只包含 '(' 和 ')' 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

```
示例 1：
输入：s = "(()"
输出：2
解释：最长有效括号子串是 "()"

示例 2：
输入：s = ")()())"
输出：4
解释：最长有效括号子串是 "()()"

示例 3：
输入：s = ""
输出：0
```


### 方法一、动态规划


定义 dp[i] 表示以下标 i 字符结尾的最长有效括号的长度。将 dp 数组全部初始化为 0 。显然有效的子串一定以 ‘)’ 结尾，因此可以知道以 ‘(’ 结尾的子串对应的 dp 值必定为 0 ，只需要求解 ‘)’ 在 dp 数组中对应位置的值。


从前往后遍历字符串求解 dp 值，每两个字符检查一次：


- s[i]=‘)’ 且 s[i−1]=‘(’，也就是字符串形如 “……()”，可以推出：dp[i]=dp[i−2]+2

- s[i]=‘)’ 且 s[i−1]=‘)’，也就是字符串形如 “……))”，可以推出：第 i-1 位置上的 ‘)’ 对应的有效括号长度为 dp[i−1]，则第 i 个位置的 ‘)’ 对应第 i−dp[i−1]−1 位置上的符号，如果其为右括号则第 i 个位置有效括号为 0。如果 s[i−dp[i−1]−1]=‘(’，那么 dp[i]=dp[i−1]+dp[i−dp[i−1]−2]+2，其中 dp[i−dp[i−1]−2] 为第 i−dp[i−1]−1 位置前的有效子串。


```java
class Solution {
    public int longestValidParentheses(String s) {
        int maxLen = 0;
        int[] dp = new int[s.length()];
        for(int i=1; i<s.length(); i++){
            if(s.charAt(i)==')'){
                if(s.charAt(i-1)=='('){
                    dp[i] = (i>=2 ? dp[i-2] : 0) + 2;
                }else if(i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1)=='('){
                    dp[i] = dp[i-1] + 2 + ((i-dp[i-1]-2)>0?dp[i-dp[i-1]-2]:0);
                }
                maxLen = Math.max(maxLen, dp[i]);
            }
        }
        return maxLen;
    }
}
```


复杂度分析

- 时间复杂度：O(n)，其中 n 为字符串的长度。只需遍历整个字符串一次，即可将 dp 数组求出来。

- 空间复杂度：O(n)。需要一个大小为 n 的 dp 数组。



### 方法二、不需要额外的空间


利用两个计数器 left 和 right 。首先，从左到右遍历字符串，对于遇到的每个 ‘(’，增加 left 计数器，对于遇到的每个 ‘)’ ，增加 right 计数器。每当 left 计数器与 right 计数器相等时，计算当前有效字符串的长度，并且记录目前为止找到的最长子字符串。当 right 计数器比 left 计数器大时，将 left 和 right 计数器同时变回 0。


这样的做法贪心地考虑了以当前字符下标结尾的有效括号长度，每次当右括号数量多于左括号数量的时候之前的字符都扔掉不再考虑，重新从下一个字符开始计算，但这样会漏掉一种情况，就是遍历的时候左括号的数量始终大于右括号的数量，即 (() ，这种时候最长有效括号是求不出来的。


解决的方法也很简单，只需要【从右往左】遍历用类似的方法计算即可，只是这个时候判断条件反了过来：

- 当 left 计数器比 right 计数器大时，将 left 和 right 计数器同时变回 0。

- 当 left 计数器与 right 计数器相等时，计算当前有效字符串的长度，并且记录目前为止找到的最长子字符串。

这样就能涵盖所有情况从而求解出答案。


```java
class Solution {
    public int longestValidParentheses(String s) {
        int left = 0, right = 0, maxLen = 0;
        for(int i=0; i<s.length(); i++){
            if(s.charAt(i)=='('){
                left++;
            }else{
                right++;
            }
            if(left==right) maxLen = Math.max(maxLen, left*2);
            if(left<right){
                left = 0;
                right = 0;
            }
        }
        left = 0;
        right = 0;
        for(int i = s.length()-1; i>=0; i--){
            if(s.charAt(i)=='('){
                left++;
            }else{
                right++;
            }
            if(left==right) maxLen = Math.max(maxLen, left*2);
            if(left>right){
                left = 0;
                right = 0;
            }
        }
        return maxLen;
    }
}
```

复杂度分析

- 时间复杂度： O(n)，其中 n 为字符串长度。只要正反遍历两边字符串即可。

- 空间复杂度： O(1)。只需要常数空间存放若干变量。



### 方法三、栈


始终保持栈底元素为当前已经遍历过的元素中「最后一个没有被匹配的右括号的下标」，这样的做法主要是考虑了边界条件的处理，栈里其他元素维护左括号的下标：


- 对于遇到的每个 ‘(’ ，将它的下标放入栈中。

- 对于遇到的每个 ‘)’ ，先弹出栈顶元素表示匹配了当前右括号：
  - 如果栈为空，说明当前的右括号为没有被匹配的右括号，将其下标放入栈中来更新之前提到的「最后一个没有被匹配的右括号的下标」
  - 如果栈不为空，当前右括号的下标减去栈顶元素即为「以该右括号为结尾的最长有效括号的长度」


需要注意的是，如果一开始栈为空，第一个字符为左括号的时候会将其放入栈中，这样就不满足提及的「最后一个没有被匹配的右括号的下标」，为了保持统一，在一开始的时候往栈中放入一个值为 −1 的元素。


```java
class Solution {
    public int longestValidParentheses(String s) {
        int maxans = 0;
        Deque<Integer> stack = new LinkedList<Integer>();
        stack.push(-1);
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else {
                stack.pop();
                if (stack.isEmpty()) {
                    stack.push(i);
                } else {
                    maxans = Math.max(maxans, i - stack.peek());
                }
            }
        }
        return maxans;
    }
}
```

复杂度分析

- 时间复杂度： O(n)，n 是给定字符串的长度。只需要遍历字符串一次即可。

- 空间复杂度： O(n)。栈的大小在最坏情况下会达到 n，因此空间复杂度为 O(n) 。

