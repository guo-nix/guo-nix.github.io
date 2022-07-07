---
title: 21. 合并两个有序链表
author: guo-nix
date: 2022-07-07 07:41:00 +0800
categories: [LeetCode, 链表]
tags: [数据结构与算法]  
math: true
---

## 问题描述


将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。提示：l1 和 l2 均按【非递减顺序】排列，-100 <= Node.val <= 100，两个链表的节点数目范围是 [0, 50]。 

<img src="./images/question_21_merge_ex1.jpg">

```
示例 1：
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]

示例 2：
输入：l1 = [], l2 = []
输出：[]

示例 3：
输入：l1 = [], l2 = [0]
输出：[0]
```


### 方法一、迭代

#### 实现一

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode head = new ListNode();
        ListNode curr = head;
        while(l1!=null && l2!=null){
            if(l1.val <=l2.val){
                curr.next = l1;
                l1 = l1.next;
            }else{
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }

        if(l1!=null) curr.next = l1;
        if(l2!=null) curr.next = l2;

        return head.next;
    }
}
```

复杂度分析

- 时间复杂度：O(n + m)。

- 空间复杂度：O(1)。



#### 实现二

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {

        ListNode l = new ListNode(-1),p=l;
        while(l1!=null || l2!=null){
            if(l1==null) {
                p.next = l2;
                break;
            }
            if(l2==null){
                p.next = l1;
                break;
            } 

            if(l1.val<=l2.val){
                p.next = l1;
                l1 = l1.next;
            }else{
                p.next = l2;
                l2 = l2.next;
            }
            p = p.next;

        }
        return l.next;
    }
}
```


### 方法二、递归


```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        } else if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```


复杂度分析

- 时间复杂度：O(n + m)。

- 空间复杂度：O(n + m)。