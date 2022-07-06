---
title: 8. 字符串转换整数
author: guo-nix
date: 2022-07-06 16:30:00 +0800
categories: [LeetCode, 字符串]
tags: [数据结构与算法]  
math: true
---

## 问题描述


请你来实现一个 myAtoi(string s) 函数，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的 atoi 函数）。`s 由英文字母（大写和小写）、数字（0-9）、' '、'+'、'-' 和 '.' 组成`。

函数 myAtoi(string s) 的算法如下：

1. 读入字符串并丢弃无用的前导空格
2. 检查下一个字符（假设还未到字符末尾）为正还是负号，读取该字符（如果有）。 确定最终结果是负数还是正数。 如果两者都不存在，则假定结果为正。
3. 读入下一个字符，直到到达下一个非数字字符或到达输入的结尾。字符串的其余部分将被忽略。
4. 将前面步骤读入的这些数字转换为整数（即，"123" -> 123， "0032" -> 32）。如果没有读入数字，则整数为 0 。必要时更改符号（从步骤 2 开始）。
5. 如果整数数超过 32 位有符号整数范围 [−231,  231 − 1] ，需要截断这个整数，使其保持在这个范围内。6. 具体来说，小于 −231 的整数应该被固定为 −231 ，大于 231 − 1 的整数应该被固定为 231 − 1 。
返回整数作为最终结果。

注意：
- 本题中的空白字符只包括空格字符 ' ' 。
- 除前导空格或数字后的其余字符串外，请勿忽略 任何其他字符。

```
示例 1：
输入：s = "42"
输出：42
解释：加粗的字符串为已经读入的字符，插入符号是当前读取的字符。
第 1 步："42"（当前没有读入字符，因为没有前导空格）
         ^
第 2 步："42"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
         ^
第 3 步："42"（读入 "42"）
           ^
解析得到整数 42 。
由于 "42" 在范围 [-231, 231 - 1] 内，最终结果为 42 。

示例 2：
输入：s = "   -42"
输出：-42
解释：
第 1 步："   -42"（读入前导空格，但忽视掉）
            ^
第 2 步："   -42"（读入 '-' 字符，所以结果应该是负数）
             ^
第 3 步："   -42"（读入 "42"）
               ^
解析得到整数 -42 。
由于 "-42" 在范围 [-231, 231 - 1] 内，最终结果为 -42 。

示例 3：
输入：s = "4193 with words"
输出：4193
解释：
第 1 步："4193 with words"（当前没有读入字符，因为没有前导空格）
         ^
第 2 步："4193 with words"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
         ^
第 3 步："4193 with words"（读入 "4193"；由于下一个字符不是一个数字，所以读入停止）
             ^
解析得到整数 4193 。
由于 "4193" 在范围 [-231, 231 - 1] 内，最终结果为 4193 。
```



### 方法一、模拟


特殊用例：
- `"+-12"`
- `"00000-42a1234"`
- `"words and 987"`
- `"   -42"`
- `" "`
- `"2147483648"`
- `"-2147483647"`

```java
class Solution {
    public int myAtoi(String s) {
        int res = 0, flag = 1;
        boolean appearNum = false;
        for(int i=0; i<s.length(); i++){
            char c = s.charAt(i);
            //去除前导空格
            while(!appearNum && i+1<s.length() && c==' '){
                i++;
                c = s.charAt(i);
            }
            //去除符号，符号可能存在多个（返回0）
            if(!appearNum && (c=='+' || c=='-')){
                if(i+1 < s.length() && !Character.isDigit(s.charAt(i+1))){
                    //"+-12"
                    return res;
                }
                if(c=='-'){
                    flag *= -1;
                }
                if(i+1 < s.length()){
                    i++;
                    //为了后面判断出现非数字字符，可直接返回。排除了"+/-"
                    c = s.charAt(i);
                }
                
            }
            //去除前导空格以及符号后，若出现非数字，直接返回
            if(!Character.isDigit(c)){
                return res;
            }
            if(Character.isDigit(c)){
                appearNum = true;
                int digit = c-'0';
                //判断边界条件!!
                if(res < (Integer.MIN_VALUE+digit)/10){
                    return Integer.MIN_VALUE;
                }else if(res > (Integer.MAX_VALUE-digit)/10){
                    return Integer.MAX_VALUE;
                }
                res = res*10+flag * digit;
            }
        }
        return res;
    }
}
```


### 方法二、自动机


**思路**

字符串处理的题目往往涉及复杂的流程以及条件情况，如果直接上手写程序，一不小心就会写出极其臃肿的代码。

因此，为了有条理地分析每个输入字符的处理方法，可以使用自动机这个概念：程序在每个时刻有一个状态 s，每次从序列中输入一个字符 c，并根据字符 c 转移到下一个状态 s'。这样，只需要建立一个覆盖所有情况的从 s 与 c 映射到 s' 的表格即可解决题目中的问题。

**算法**

本题可以建立如下图所示的自动机：

<img src="./images/8_fig1.png">

也可以用下面的表格来表示这个自动机：


|	|' '	|+/-	|number|	other|
|  ----  | ----  | ----  | ----  | ----  |
|start|	start|	signed|	in_number|	end|
|signed|	end|	end|	in_number|	end|
|in_number|	end	|end|	in_number|	end|
|end|	end	|end|	end	|end|


接下来编程部分就非常简单了：只需要把上面这个状态转换表抄进代码即可。

另外自动机也需要记录当前已经输入的数字，只要在 s' 为 in_number 时，更新输入的数字，即可最终得到输入的数字。


```java
class Solution {
    public int myAtoi(String str) {
        Automaton automaton = new Automaton();
        int length = str.length();
        for (int i = 0; i < length; ++i) {
            automaton.get(str.charAt(i));
        }
        return (int) (automaton.sign * automaton.ans);
    }
}

class Automaton {
    public int sign = 1;
    public long ans = 0;
    private String state = "start";
    private Map<String, String[]> table = new HashMap<String, String[]>() {{
        put("start", new String[]{"start", "signed", "in_number", "end"});
        put("signed", new String[]{"end", "end", "in_number", "end"});
        put("in_number", new String[]{"end", "end", "in_number", "end"});
        put("end", new String[]{"end", "end", "end", "end"});
    }};

    public void get(char c) {
        state = table.get(state)[get_col(c)];
        if ("in_number".equals(state)) {
            ans = ans * 10 + c - '0';
            ans = sign == 1 ? Math.min(ans, (long) Integer.MAX_VALUE) : Math.min(ans, -(long) Integer.MIN_VALUE);
        } else if ("signed".equals(state)) {
            sign = c == '+' ? 1 : -1;
        }
    }

    private int get_col(char c) {
        if (c == ' ') {
            return 0;
        }
        if (c == '+' || c == '-') {
            return 1;
        }
        if (Character.isDigit(c)) {
            return 2;
        }
        return 3;
    }
}
```


复杂度分析

- 时间复杂度：O(n)，其中 n 为字符串的长度。只需要依次处理所有的字符，处理每个字符需要的时间为 O(1)。

- 空间复杂度：O(1)。自动机的状态只需要常数空间存储。




















