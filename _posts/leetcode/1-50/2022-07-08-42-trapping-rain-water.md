---
title: 10. 正则表达式匹配
author: guo-nix
date: 2022-07-08 08:30:00 +0800
categories: [LeetCode, 字符串]
tags: [数据结构与算法]  
math: true
---

## 问题描述
## 42. 接雨水


给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

<img src="./images/question_42_rainwatertrap.png">

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。
```

### 方法一、动态规划

对于下标 i，下雨后水能到达的最大高度等于下标 i 两边的最大高度的最小值，下标 i 处能接的雨水量等于下标 i 处的水能到达的最大高度减去 height[i]。

创建两个长度为 n 的数组 leftMax 和 rightMax。对于 0≤i<n，leftMax[i] 表示下标 i 及其左边的位置中，height 的最大高度，rightMax[i] 表示下标 i 及其右边的位置中，height 的最大高度。

显然，leftMax[0]=height[0]，rightMax[n−1]=height[n−1]。两个数组的其余元素的计算如下：

- 当 1≤i≤n−1 时，leftMax[i]=max(leftMax[i−1],height[i])；

- 当 0≤i≤n−2 时，rightMax[i]=max(rightMax[i+1],height[i])。


因此可以正向遍历数组 height 得到数组 leftMax 的每个元素值，反向遍历数组 height 得到数组 rightMax 的每个元素值。


在得到数组 leftMax 和 rightMax 的每个元素值之后，对于 0≤i<n，下标 i 处能接的雨水量等于 min(leftMax[i],rightMax[i])−height[i]。遍历每个下标位置即可得到能接的雨水总量。


```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        if(n==0) return 0;

        int[] leftMax = new int[n];
        leftMax[0] = height[0];
        for(int i=1; i<n; i++){
            leftMax[i] = Math.max(leftMax[i-1],height[i]);
        }

        int[] rightMax = new int[n];
        rightMax[n-1] = height[n-1];
        for(int j = n-2; j>=0; j--){
            rightMax[j] = Math.max(rightMax[j+1], height[j]);
        }

        int ans = 0;
        for(int i=0; i<n; i++){
            ans += Math.min(leftMax[i], rightMax[i]) - height[i];
        }

        return ans;
    }
}
```


复杂度分析

- 时间复杂度：O(n)，其中 n 是数组 height 的长度。计算数组 leftMax 和 rightMax 的元素值各需要遍历数组 height 一次，计算能接的雨水总量还需要遍历一次。

- 空间复杂度：O(n)，其中 n 是数组 height 的长度。需要创建两个长度为 n 的数组 leftMax 和 rightMax。



### 方法二、单调栈

维护一个单调栈，单调栈存储的是下标，满足从栈底到栈顶的下标对应的数组 \textit{height}height 中的元素递减。


从左到右遍历数组，遍历到下标 i 时，如果栈内至少有两个元素，记栈顶元素为 top，top 的下面一个元素是 left，则一定有 height[left]≥height[top]。如果 height[i]>height[top]，则得到一个可以接雨水的区域，该区域的宽度是 i−left−1，高度是 min(height[left],height[i])−height[top]，根据宽度和高度即可计算得到该区域能接的雨水量。


为了得到 left，需要将 top 出栈。在对 top 计算能接的雨水量之后，left 变成新的 top，重复上述操作，直到栈变为空，或者栈顶下标对应的 height 中的元素大于或等于 height[i]。


在对下标 i 处计算能接的雨水量之后，将 i 入栈，继续遍历后面的下标，计算能接的雨水量。遍历结束之后即可得到能接的雨水总量。


<img src="./images/42_fig1.png">


```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        Deque<Integer> stack = new LinkedList<Integer>();
        int n = height.length;
        for (int i = 0; i < n; ++i) {
            while (!stack.isEmpty() && height[i] > height[stack.peek()]) {
                int top = stack.pop();
                if (stack.isEmpty()) {
                    break;
                }
                int left = stack.peek();
                int currWidth = i - left - 1;
                int currHeight = Math.min(height[left], height[i]) - height[top];
                ans += currWidth * currHeight;
            }
            stack.push(i);
        }
        return ans;
    }
}
```

复杂度分析

- 时间复杂度：O(n)，其中 n 是数组 height 的长度。从 0 到 n−1 的每个下标最多只会入栈和出栈各一次。

- 空间复杂度：O(n)，其中 n 是数组 height 的长度。空间复杂度主要取决于栈空间，栈的大小不会超过 n。




### 方法三、双指针


动态规划的做法中，需要维护两个数组 leftMax 和 rightMax，因此空间复杂度是 O(n)。是否可以将空间复杂度降到 O(1)？



注意到下标 i 处能接的雨水量由 leftMax[i] 和 rightMax[i] 中的最小值决定。由于数组 leftMax 是从左往右计算，数组 rightMax 是从右往左计算，因此可以使用双指针和两个变量代替两个数组。

维护两个指针 left 和 right，以及两个变量 leftMax 和 rightMax，初始时 left=0,right=n−1,leftMax=0,rightMax=0。指针 left 只会向右移动，指针 right 只会向左移动，在移动指针的过程中维护两个变量 leftMax 和 rightMax 的值。

当两个指针没有相遇时，进行如下操作：

- 使用 height[left] 和 height[right] 的值更新 leftMax 和 rightMax 的值；

- 如果 height[left]<height[right]，则必有 leftMax<rightMax，下标 left 处能接的雨水量等于 leftMax−height[left]，将下标 left 处能接的雨水量加到能接的雨水总量，然后将 left 加 1（即向右移动一位）；

- 如果 height[left]≥height[right]，则必有 leftMax≥rightMax，下标 right 处能接的雨水量等于 rightMax−height[right]，将下标 right 处能接的雨水量加到能接的雨水总量，然后将 right 减 1（即向左移动一位）。


当两个指针相遇时，即可得到能接的雨水总量。


```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        while (left < right) {
            leftMax = Math.max(leftMax, height[left]);
            rightMax = Math.max(rightMax, height[right]);
            if (height[left] < height[right]) {
                ans += leftMax - height[left];
                ++left;
            } else {
                ans += rightMax - height[right];
                --right;
            }
        }
        return ans;
    }
}

```


复杂度分析

- 时间复杂度：O(n)，其中 n 是数组 height 的长度。两个指针的移动总次数不超过 n。

- 空间复杂度：O(1)。只需要使用常数的额外空间。




