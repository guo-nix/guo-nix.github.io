---
title: 19. 删除链表的倒数第 N 个结点
author: guo-nix
date: 2022-07-06 18:39:00 +0800
categories: [LeetCode, 链表]
tags: [数据结构与算法]  
math: true
---

## 问题描述


给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

<img src="./images/question_19_remove_ex1.jpg">

提示：
- 链表中结点的数目为 sz
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`
 
进阶：你能尝试使用一趟扫描实现吗？


```
示例 1：
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]

示例 2：
输入：head = [1], n = 1
输出：[]

示例 3：
输入：head = [1,2], n = 1
输出：[1]
```


### 方法一、计算链表长度


首先从头节点开始对链表进行一次遍历，得到链表的长度 L。随后再从头节点开始对链表进行一次遍历，当遍历到第 L−n+1 个节点时，它就是需要删除的节点。


为了与题目中的 n 保持一致，节点的编号从 1 开始，头节点为编号 1 的节点。

为了方便删除操作，从哑节点开始遍历 L−n+1 个节点。当遍历到第 L−n+1 个节点时，它的下一个节点就是需要删除的节点，这样只需要修改一次指针，就能完成删除操作。


```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dump = new ListNode();
        dump.next = head;
        int len = getLength(head);
        if(n>len) return head;
        ListNode cur = dump;
        for(int i=1; i<len-n+1;i++){
            cur = cur.next;
        }
        cur.next = cur.next.next;
        return dump.next;
    }

    public int getLength(ListNode head){
        int len = 0;
        while(head!=null){
            len++;
            head = head.next;
        }
        return len;
    }
}
```


复杂度分析

- 时间复杂度：O(L)，其中 L 是链表的长度。

- 空间复杂度：O(1)。


### 方法二、双指针(ok)

由于需要找到倒数第 n 个节点，因此可以使用两个指针 first 和 second 同时对链表进行遍历，并且 first 比 second 超前 n 个节点。当 first 遍历到链表的末尾时，second 就恰好处于倒数第 n 个节点。

具体地，初始时 first 和 second 均指向头节点。首先使用 first 对链表进行遍历，遍历的次数为 n。此时，first 和 second 之间间隔了 n−1 个节点，即 first 比 second 超前了 n 个节点。

在这之后，同时使用 first 和 second 对链表进行遍历。当 first 遍历到链表的末尾（即 first 为空指针）时，second 恰好指向倒数第 n 个节点。

如果能够得到的是倒数第 n 个节点的【前驱节点】而不是倒数第 n 个节点的话，删除操作会更加方便。因此可以考虑在初始时将 second 指向【哑节点】，其余的操作步骤不变。这样一来，当 first 遍历到链表的末尾时，second 的下一个节点就是需要删除的节点。

<img src="./images/19_fig1.png">

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        ListNode first = head;
        ListNode second = dummy;
        int i=0;
        for(; i<n && first!=null; i++){
            first = first.next;
        }
        if(i<n){
            return head;
        }
        while(first!=null){
            first = first.next;
            second = second.next;
        }
        second.next = second.next.next;
        return dummy.next;
    }
}
```

复杂度分析

- 时间复杂度：O(L)，其中 L 是链表的长度。

- 空间复杂度：O(1)。


### 方法三、栈

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        Deque<ListNode> stack = new LinkedList<ListNode>();
        ListNode cur = dummy;
        while (cur != null) {
            stack.push(cur);
            cur = cur.next;
        }
        for (int i = 0; i < n; ++i) {
            stack.pop();
        }
        ListNode prev = stack.peek();
        prev.next = prev.next.next;
        ListNode ans = dummy.next;
        return ans;
    }
}
```