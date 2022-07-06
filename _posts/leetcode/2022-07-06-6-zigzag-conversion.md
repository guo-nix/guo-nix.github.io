---
title: 6. Z 字形变换
author: guo-nix
date: 2022-07-06 15:10:00 +0800
categories: [LeetCode, 字符串]
tags: [数据结构与算法]  
math: true
---

## 问题描述


将一个给定字符串 s 根据给定的行数 `numRows`，以从上往下、从左到右进行 Z 字形排列。s 由`英文字母（小写和大写）、',' 和 '.'` 组成。

比如输入字符串为 `"PAYPALISHIRING"` 行数为 3 时，排列如下：

```
P   A   H   N
A P L S I I G
Y   I   R
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如:`"PAHNAPLSIIGYIR"`。

请你实现这个将字符串进行指定行数变换的函数：`string convert(string s, int numRows);`

```
示例 1：
输入：s = "PAYPALISHIRING", numRows = 3
输出："PAHNAPLSIIGYIR"

示例 2：
输入：s = "PAYPALISHIRING", numRows = 4
输出："PINALSIGYAHRPI"
解释：
P     I    N
A   L S  I G
Y A   H R
P     I

示例 3：
输入：s = "A", numRows = 1
输出："A"
```
{: file="示例"}


### 方法一、利用二维矩阵模拟(不推荐)


设 n 为字符串 s 的长度，`r=numRows`。对于 `r=1`（只有一行）或者 `r≥n`（只有一列）的情况，答案与 s 相同，可以直接返回 s。对于其余情况，考虑创建一个二维矩阵，然后在矩阵上按 Z 字形填写字符串 s，最后逐行扫描矩阵中的非空字符，组成答案。

根据题意，当在矩阵上填写字符时，会向下填写 r 个字符，然后向右上继续填写 `r−2` 个字符，最后回到第一行，因此 Z 字形变换的周期 `t=r+r-2=2r-2`，每个周期会占用矩阵上的 `1+r-2=r-1` 列。


![](/assets/img/leetcode/1-50/6_fig1.png){: width="300" height="250" }

因此有 `⌈t/n⌉` 个周期（最后一个周期视作完整周期），乘上每个周期的列数，得到矩阵的列数 `c=⌈t/n⌉⋅(r−1)`。

创建一个 r 行 c 列的矩阵，然后遍历字符串 s 并按 Z 字形填写。具体来说，设当前填写的位置为 `(x,y)`，即矩阵的 x 行 y 列。初始 `(x,y)=(0,0)`，即矩阵左上角。若当前字符下标 i 满足 `i mod t<r−1`，则向下移动，否则向右上移动。

填写完成后，逐行扫描矩阵中的非空字符，组成答案。


```java
class Solution {
    public String convert(String s, int numRows) {
        int n = s.length(), r = numRows;
        if (r == 1 || r >= n) {
            return s;
        }
        int t = r * 2 - 2;
        int c = (n / t + 1) * (r - 1);
        char[][] mat = new char[r][c];
        for (int i = 0, x = 0, y = 0; i < n; ++i) {
            mat[x][y] = s.charAt(i);
            if (i % t < r - 1) {
                ++x; // 向下移动
            } else {
                --x;
                ++y; // 向右上移动
            }
        }
        StringBuffer ans = new StringBuffer();
        for (char[] row : mat) {
            for (char ch : row) {
                if (ch != 0) {
                    ans.append(ch);
                }
            }
        }
        return ans.toString();
    }
}
```

复杂度分析

- 时间复杂度：`O(r⋅n)`，其中 `r=numRows`，n 为字符串 s 的长度。时间主要消耗在矩阵的创建和遍历上，矩阵的行数为 r，列数可以视为 O(n)。

- 空间复杂度：`O(r⋅n)`。矩阵需要 `O(r⋅n)` 的空间。



### 方法二、压缩矩阵空间

方法一中的矩阵有大量的空间没有被使用，能否优化呢？

注意到每次往矩阵的某一行添加字符时，都会添加到该行上一个字符的右侧，且最后组成答案时只会用到每行的非空字符。因此可以将矩阵的每行初始化为一个空列表，每次向某一行添加字符时，添加到该行的列表末尾即可。


```java
class Solution {
    public String convert(String s, int numRows) {
        int n = s.length(), r = numRows;
        if (r == 1 || r >= n) {
            return s;
        }
        StringBuffer[] mat = new StringBuffer[r];
        for (int i = 0; i < r; ++i) {
            //对每一项初始化
            mat[i] = new StringBuffer();
        }
        for (int i = 0, x = 0, t = r * 2 - 2; i < n; ++i) {
            mat[x].append(s.charAt(i));
            if (i % t < r - 1) {
                ++x;
            } else {
                --x;
            }
        }
        StringBuffer ans = new StringBuffer();
        for (StringBuffer row : mat) {
            ans.append(row);
        }
        return ans.toString();
    }
}
```


复杂度分析

- 时间复杂度：`O(n)`。

- 空间复杂度：`O(n)`。压缩后的矩阵需要 `O(n)` 的空间。



### 方法三、直接构造

研究方法一中矩阵的每个非空字符会对应到 s 的哪个下标（记作idx），从而直接构造出答案。

由于 Z 字形变换的周期为 `t=2r-2`，因此对于矩阵第一行的非空字符，其对应的 idx 均为 t 的倍数，即 `idx≡0(modt)`；同理，对于矩阵最后一行的非空字符，应满足 `idx≡r−1(modt)`。

对于矩阵的其余行（行号设为 i），每个周期内有两个字符，第一个字符满足 `idx≡i(modt)`，第二个字符满足 `idx≡t−i(modt)`。



```
0             0+t                    0+2t                     0+3t
1      t-1    1+t            0+2t-1  1+2t            0+3t-1   1+3t
2  t-2        2+t  0+2t-2            2+2t  0+3t-2             2+3t  
3             3+t                    3+2t                     3+3t
```


```c++
class Solution {
public:
    string convert(string s, int numRows) {
        int n = s.length(), r = numRows;
        if (r == 1 || r >= n) {
            return s;
        }
        string ans;
        int t = r * 2 - 2;
        for (int i = 0; i < r; ++i) { // 枚举矩阵的行
            for (int j = 0; j + i < n; j += t) { // 枚举每个周期的起始下标
                ans += s[j + i]; // 当前周期的第一个字符
                if (0 < i && i < r - 1 && j + t - i < n) {
                    ans += s[j + t - i]; // 当前周期的第二个字符
                }
            }
        }
        return ans;
    }
};
```

复杂度分析

- 时间复杂度：`O(n)`，其中 n 为字符串 s 的长度。s 中的每个字符仅会被访问一次，因此时间复杂度为 `O(n)`。

- 空间复杂度：`O(1)`。返回值不计入空间复杂度。



















