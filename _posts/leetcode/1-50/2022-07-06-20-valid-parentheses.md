---
title: 20. 有效的括号
author: guo-nix
date: 2022-07-06 18:40:00 +0800
categories: [LeetCode, XXX]
tags: [数据结构与算法]  
math: true
---

## 问题描述


```
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：
- 左括号必须用相同类型的右括号闭合。
- 左括号必须以正确的顺序闭合。

示例 1：
输入：s = "()"
输出：true

示例 2：
输入：s = "()[]{}"
输出：true

示例 3：
输入：s = "(]"
输出：false

示例 4：
输入：s = "([)]"
输出：false

示例 5：
输入：s = "{[]}"
输出：true

相关标签：字符串、栈
```


### 方法

stack 没有 top 函数！请使用 peek 函数。

#### 实现一

```java
class Solution {
    public boolean isValid(String s) {
        int n = s.length();
        if(n==0) return true;
        if(n%2==1) return false;
        Map<Character, Character> map = new HashMap<>();
        map.put(')', '(');
        map.put(']', '[');
        map.put('}', '{');
        Stack<Character> stack = new Stack<>();
        for(Character c : s.toCharArray()){
            if(c=='(' || c=='{' || c=='['){
                stack.push(c);
            }else{
                if(stack.isEmpty() || stack.pop()!=map.get(c)) return false;
            }

        }
        return stack.isEmpty();

    }
}
```

#### 实现二

```java
class Solution {
    public boolean isValid(String s) {
        int n = s.length();
        if(n==0) return true;
        if(n%2==1) return false;

        Map<Character, Character> map = new HashMap<>();
        map.put(')','(');
        map.put(']','[');
        map.put('}','{');
        Stack<Character> stack = new Stack<>();
        for(int i=0; i<n; i++){
            char c = s.charAt(i);
            if(c=='(' || c=='[' || c=='{'){
                stack.push(c);
            }else if(stack.isEmpty() || stack.peek()!=map.get(c)){
                return false;
            }else{
                stack.pop();
            }
        }
        //不能直接返回true
        return stack.isEmpty();
    }
}
```

#### 实现三

```java
class Solution {
    public boolean isValid(String s) {
        int n = s.length();
        if(n==0) return true;
        if(n%2==1) return false;
        Stack<Character> stack = new Stack<>();
        Map<Character, Character> map = new HashMap<>();
        map.put(')','(');
        map.put(']','[');
        map.put('}','{');

        for(int i=0; i<n; i++){
            char c = s.charAt(i);
            if(c=='(' || c=='{' || c=='['){
                stack.push(c);
            }else{
                if(!stack.isEmpty() && stack.peek()==map.get(c)){
                    stack.pop();
                }else{
                    return false;
                } 
             
            }
        }
        return stack.isEmpty();

    }
}
```