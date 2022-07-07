---
title: 2. 两数相加
author: guo-nix
date: 2022-07-06 12:30:00 +0800
categories: [LeetCode, 链表]
tags: [数据结构与算法]  
math: true
---

## 问题描述


给出两个**非空**的链表用来表示两个非负的整数。其中，它们各自的位数是按照 [逆序] 的方式存储的，并且它们的每个节点只能存储 一位 数字。 

如果，将这两个数相加起来，则会返回一个新的链表来表示它们的和。 

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。 


```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```
{: file="示例： (高位在后)" }

Related Topics 链表 数学 


### 方法一、模拟

思路：
1. 由于输入的两个链表都是逆序存储数字的位数的，因此两个链表中同一位置的数字可以直接相加。
2. 同时遍历两个链表，逐位计算它们的和，并与当前位置的进位值相加。和为 `n1+n2+carry`；其中，答案链表处相应位置的数字为 `(n1+n2+carry)%10`，而新的进位值为 `(n1+n2+carry)/10`。
3. 如果两个链表的长度不同，则可以认为长度短的链表的后面有若干个 0。
4. 此外，如果链表遍历结束后，有 `carry>0`，还需要在答案链表的后面附加一个节点，节点的值为 carry。

#### 1) 头头节点

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int carry = 0;
        ListNode head = new ListNode();
        ListNode curr = head;
        while(l1!=null || l2!=null){
            int node1 = (l1==null)? 0: l1.val;
            int node2 = (l2==null)? 0: l2.val;
            int v = (node1 + node2 + carry) % 10;
            carry = (node1 + node2 + carry) / 10;
            curr.next = new ListNode(v);
            curr = curr.next;
            if(l1!=null) l1 = l1.next;
            if(l2!=null) l2 = l2.next;
        }
        //!!!
        if(carry>0) curr.next = new ListNode(carry);
        return head.next;
    }
}
```

#### 2) 头节点

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = null, tail = null;
        int carry = 0;
        while(l1!=null || l2!=null){
            int n1 = l1!=null? l1.val: 0;
            int n2 = l2!=null? l2.val: 0;
            int sum = n1 + n2 + carry;
            if(head==null){
                head = tail = new ListNode(sum % 10);
            }else{
                tail.next = new ListNode(sum % 10);
                tail = tail.next;
            }
            carry = sum/10;
            if(l1!=null) l1 = l1.next;
            if(l2!=null) l2 = l2.next;
        }
        if(carry>0) tail.next = new ListNode(carry);
        return head;
    }
}
```

**复杂度分析**

* 时间复杂度：`O(max(m,n))`，其中 `m,n` 为两个链表的长度。要遍历两个链表的全部位置，而处理每个位置只需要 O(1) 的时间。
* 空间复杂度：`O(max(m,n))`。答案链表的长度最多为较长链表的长度 +1。



### 方法二、递归

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        return recursive(l1, l2, 0);
    }

    private ListNode recursive(ListNode l1, ListNode l2, int carry) {
        if (l1 == null && l2 == null && carry == 0) {   // 递归结束条件
            return null;
        }
        int l1Val = l1 == null ? 0 : l1.val;
        int l2Val = l2 == null ? 0 : l2.val;
        int curSum = l1Val + l2Val + carry;
        ListNode curNode = new ListNode(curSum % 10);
        curNode.next = recursive(l1 == null ? null : l1.next, 
            l2 == null ? null : l2.next, curSum / 10);
        return curNode;
    }
}
```


