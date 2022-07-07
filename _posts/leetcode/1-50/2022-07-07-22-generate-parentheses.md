---
title: 22. 括号生成
author: guo-nix
date: 2022-07-07 08:30:00 +0800
categories: [LeetCode, XXX]
tags: [数据结构与算法]  
math: true
---

## 问题描述



```
数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且[有效的]括号组合。有效括号组合需满足：左括号必须以正确的顺序闭合。

示例 1：
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]

示例 2：
输入：n = 1
输出：["()"]
 
提示：1 <= n <= 8

相关标签：字符串、动态规划、回溯
```


### 方法一、回溯法

只在序列仍然保持有效时才添加 '(' or ')'，如果左括号数量不大于 n，可以放一个左括号。如果右括号数量小于左括号的数量，可以放一个右括号。


```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> ans = new ArrayList<>();
        backtrack(ans, new StringBuilder(), 0, 0, n);
        return ans;
    }

    public void backtrack(List<String> ans, StringBuilder cur, int open, int close, int max) {
        if(cur.length()==max*2){
            ans.add(cur.toString());
            return;
        }
        if(open<max){
            cur.append('(');
            backtrack(ans, cur, open+1, close, max);
            cur.deleteCharAt(cur.length()-1);
        }
        //右括号的数量<=左括号！
        if(close<open){   //注意此处不是 if(close<max)！！
            cur.append(')');
            backtrack(ans, cur, open, close+1, max);
            cur.deleteCharAt(cur.length()-1);
        }
    }

}
```

<img src="./images/22_fig1.png">


### 方法二、暴力法(不推荐)


生成所有序列，然后检查序列是否有效。

1）为了生成所有序列，可以使用递归。长度为 n 的序列就是在长度为 n-1 的序列前加一个 '(' 或 ')'。

2）为了检查序列是否有效，遍历这个序列，并使用一个变量 balance 表示左括号的数量减去右括号的数量。<font color='red'>**如果在遍历过程中 balance 的值小于零，或者结束时 balance 的值不为零，那么该序列就是无效的，否则它是有效的。**</font>  

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> ans = new ArrayList<>();
        generateAll(new char[n*2], 0, ans);
        return ans;
    }

    public void generateAll(char[] current, int pos, List<String> result){
        if(pos==current.length){
            if(valid(current)) result.add(new String(current));
            return;
        }
        current[pos]='(';
        generateAll(current, pos+1, result);
        current[pos]=')';
        generateAll(current, pos+1, result);
    }

    public boolean valid(char[] current){
        int balance = 0;
        for(char c: current){
            if(c=='('){
                balance++;
            }else{
                balance--;
            }
            if(balance<0) return false;
        }
        return balance==0;
    }
}
```

复杂度分析

- 时间复杂度：O(2^{2n}n)，对于 2^{2n}个序列中的每一个，用于建立和验证该序列的复杂度为 O(n)。

- 空间复杂度：O(n)，除了答案数组之外，所需要的空间取决于递归栈的深度，每一层递归函数需要 O(1) 的空间，最多递归 2n 层，因此空间复杂度为 O(n)。


### 方法三、按括号序列的长度递归

任何一个括号序列都一定是由 ( 开头，并且第一个 ( 一定有一个唯一与之对应的 )。这样一来，每一个括号序列可以用 (a)b 来表示，其中 a 与 b 分别是一个合法的括号序列（可以为空）。

那么，要生成所有长度为 2 * n 的括号序列，定义一个函数 generate(n) 来返回所有可能的括号序列。那么在函数 generate(n) 的过程中：

- 需要枚举与第一个 ( 对应的 ) 的位置 2 * i + 1；
- 递归调用 generate(i) 即可计算 a 的所有可能性；
- 递归调用 generate(n - i - 1) 即可计算 b 的所有可能性；
- 遍历 a 与 b 的所有可能性并拼接，即可得到所有长度为 2 * n 的括号序列。

为了节省计算时间，在每次 generate(i) 函数返回之前，把返回值存储起来，下次再调用 generate(i) 时可以直接返回，不需要再递归计算。

```java
class Solution {
    ArrayList[] cache = new ArrayList[100];

    public List<String> generate(int n) {
        if (cache[n] != null) {
            return cache[n];
        }
        ArrayList<String> ans = new ArrayList<String>();
        if (n == 0) {
            ans.add("");
        } else {
            for (int c = 0; c < n; ++c) {
                for (String left: generate(c)) {
                    for (String right: generate(n - 1 - c)) {
                        ans.add("(" + left + ")" + right);
                    }
                }
            }
        }
        cache[n] = ans;
        return ans;
    }

    public List<String> generateParenthesis(int n) {
        return generate(n);
    }
}
```

时空复杂度均为 O(4^n/sqrt(n))。