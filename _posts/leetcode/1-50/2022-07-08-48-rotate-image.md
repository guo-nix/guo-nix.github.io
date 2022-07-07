---
title: 10. 正则表达式匹配
author: guo-nix
date: 2022-07-08 08:30:00 +0800
categories: [LeetCode, 字符串]
tags: [数据结构与算法]  
math: true
---

## 问题描述
## 48. 旋转图像

给定一个 n × n 的二维矩阵 matrix 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在【原地】旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。其中，1 <= n <= 20。

<img src="question_48_mat1.jpg">

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

<img src="question_48_mat2.jpg">

```
输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```


### 方法一、原地旋转


<img src="./images/48_fig2.png">

<img src="./images/48_fig3.png">

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        for (int i = 0; i < n / 2; ++i) {
            for (int j = 0; j < (n + 1) / 2; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][i];
                matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
                matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
                matrix[j][n - i - 1] = temp;
            }
        }
    }
}
```

复杂度分析

- 时间复杂度：O(N^2)，其中 N 是 matrix 的边长。需要枚举的子矩阵大小为 O(⌊n/2⌋×⌊(n+1)/2⌋)=O(N^2)。

- 空间复杂度：O(1)。为原地旋转。




### 方法二、用翻转代替旋转

<img src="./images/48_fig4.png">

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 水平翻转
        for (int i = 0; i < n / 2; ++i) {
            for (int j = 0; j < n; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - i - 1][j];
                matrix[n - i - 1][j] = temp;
            }
        }
        // 主对角线翻转
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
    }
}
```


复杂度分析

- 时间复杂度：O(N^2)，其中 N 是 matrix 的边长。对于每一次翻转操作，都需要枚举矩阵中一半的元素。

- 空间复杂度：O(1)。为原地翻转得到的原地旋转。




### 方法三、使用辅助数组

【不推荐】

<img src="./images/48_fig1.png">


可以得到规律：

> 对于矩阵中第 i 行的第 j 个元素，在旋转后，它出现在倒数第 i 列的第 j 个位置。

由于矩阵中的行列从 0 开始计数，因此对于矩阵中的元素 matrix[row][col]，在旋转后，它的新位置为 matrix_new[col][n−row−1]。在遍历完成之后，再将 matrix_new 中的结果复制到原数组中即可。


```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        int[][] matrix_new = new int[n][n];
        for(int i=0;i<n;i++){
            for (int j = 0; j < n; ++j) {
                matrix_new[j][n - i - 1] = matrix[i][j];
            }
        }
        //拷贝回原数组
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                matrix[i][j] = matrix_new[i][j];
            }
        }
    }
}
```

复杂度分析

- 时间复杂度：O(N^2)，其中 N 是 matrix 的边长。

- 空间复杂度：O(N^2)。需要使用一个和 matrix 大小相同的辅助数组。


