---
title: Leetcode之旅|链表啊，链表! (2)
date: 2018-01-18 22:25:08
tags:
    - Leetcode
    - Data Structure
    - Linked list
    - 随笔
mathjax: true
---

　　今天记录的是两道特别有意思的题：判断链表中存不存在环(circle)。为了找解法，在纸上画了各种数字、箭头、圈圈和推导，还以为在研究什么彩票号码呢哈哈 ~ ~ ~

## 题目描述

　　和昨天一样，今天的两道题同样是分为基础版和进阶版，不同的是，今天的两个版本可以看作是一道大题的两小问，二者关联性更强。

>　　基础版：
> 　　
> 　　　　判断链表是否存在环。([141. Linked list cycle](https://leetcode.com/problems/linked-list-cycle/description//))
> 　
>　　进阶版：
>　　
>　　　　若存在环，找出环的起点。([142. Linked list cycle II](https://leetcode.com/problems/linked-list-cycle-ii/description/))　　 　 　

　　另外，要求在不修改链表的同时，空间复杂度为$O(1)$。

## 解题思路

### 基础版

　　这道题我一开始是联想到了之前学习DFS的思路，即遍历每个节点，并在访问后做上visited的标记，如果访问到一个已经标记的节点，说明有环。然而想法是美好的，不，也并不美好，毕竟这个空间复杂度不是$O(1)$，而且发现没那么好实现，因为单向链表的特点就是不能回头，你很容易知道一个节点的下一个节点是啥，但前一个节点是啥？不能直接知道。

　　后来逛了下别人的答案，感觉自己被羞辱了，思路完全不一样啊！完全没这么想啊！看来还是经验不够啊！(受打击三连)又学习到了。具体怎么做呢？需要用到两个指针，一个每次走一步(slow)，另一个走得快一点，每次走两步(fast)，咳咳，重点来了 ~ 如果一个链表不存在环，那总会走向终点(遇到Null)；如果存在环呢？那两个指针会一直走下去，但这样循环没法终止，就没法返回结果，所以进一步的判断依据是，如果存在环，slow和fast两个指针会相遇，一旦相遇，循环终止，即可返回结果。代码如下：

```python
def hasCycle(head):
        """
        :type head: ListNode
        :rtype: bool
        """
        slow, fast = head, head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow==fast:
                return True
        return False
```

### 进阶版

　　虽然有了基础版的打底，但进阶版也折腾了我很久，最后还是借了别人的智慧才解决的，唉，心塞。 和基础版一样，我们需要设置两个指针，均初始化为head(如果有的话)，因为没有环的情况很好判断，我们接下来只讨论在有环的情况下，如何找环的起始点。

　　将head看作第一个节点，那么如果走$T-1$步的话，slow将会到达第$T$个节点，而fast会到达第$2T-1$个节点。当二者第一次相遇(meet)，设在第$M$个节点，那么slow和fast的距离为$M-1$个节点，那么$M-1$一定是环的节点个数($C$)的整数倍，即：

$$M-1 = nC, n \ge 1$$

这时候我们再设一个指针_head，初始为第一个节点的位置(head),那么此时slow(或者fast,因为循环终止条件是二者相同)与_head的距离为$nC$。此时再分两种情况讨论。

>　　如果第一次相遇的点就是head，那么说明head就是环的入口,直接返回head即可。这点很好理解。
>　　
>　　如果第一次相遇的不是head，,那么让_head和slow以相同速度前进，当_head到达环的入口时，因为slow和head的距离为$nC$，那么二者必然相遇。 因为对于_head而言，之前没有相遇的机会，所以第一次相遇的点必然是入口，返回_head即可。

　　代码如下：

```python
def detectCycle(head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head:
            return None
        slow, fast = head, head
        
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            
            if slow == fast:
                break
        
        if not fast or not fast.next:
            return None
        
        if head == slow:
            return head
        _head = head
        while _head != slow:
            _head = _head.next
            slow = slow.next
        return _head
```

## 总结

　　刷完链表就刷树和图吧，数据结构太有意思了。记得之前我关注的一个博主曾在合理使用堆栈给出某道题很elegant的解法时感慨：“啊，这就是数据结构的胜利！！！”而我目前还只是在练习一些很简单的操作，希望我以后也能将它们变为趁手的利刃吧。