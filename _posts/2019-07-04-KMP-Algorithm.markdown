---
layout:     post
title:      "Knuth-Morris-Pratt (KMP) 算法原理、实现及其优化"
subtitle:   " \"写在研究毫无进展的充电时刻\""
date:       2019-07-04 12:00:00
author:     "小兴安岭"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - 算法
    - KMP
---

> “Always bear in mind that your own resolution to succeed is more important than any other.”


## 前言

KMP算法一直是字符串匹配中经常提到的算法，它能避免不必要的字符比较从而减少字符串匹配所需的时间。其中KMP的全称是Knuth-Morris-Pratt，从 [维基百科](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm) 可知，该算法由 Donald Knuth，James H. Morris 以及 Vaughan Pratt 同时提出。
> The [algorithm](https://en.wikipedia.org/wiki/Algorithm) was conceived in 1970 by [Donald Knuth](https://en.wikipedia.org/wiki/Donald_Knuth) and [Vaughan Pratt](https://en.wikipedia.org/wiki/Vaughan_Pratt), and independently by [James H. Morris](https://en.wikipedia.org/wiki/James_H._Morris). 
>

很早以前我就想对KMP算法的原理以及实现做一个总结，但由于教材上晦涩难懂的介绍以及自身的拖延症属性，这项工作一直拖到了现在。在一个百无聊赖的下午，我看到了 [jakeboxer 关于KMP算法的博客](http://jakeboxer.com/blog/2009/12/13/the-knuth-morris-pratt-algorithm-in-my-own-words/)，博客里关于部分匹配表的介绍完美地解释了KMP算法的原理，在此基础上，我将使用自己的语言介绍一下KMP算法具体的代码实现以及可优化空间。




## KMP算法

### 简单字符串匹配算法
在介绍KMP算法前，我想介绍一种原始的字符串匹配算法——简单字符串匹配算法。当我们在串 S 中找串 P 时，串 S 被称为主串，串 P 被称为子串。简单字符串匹配算法可以总结为一句话：在一趟从主串的某一下标开始的字符比较中，如果出现主串和子串对应的字符不匹配，那么主串的下标将前移一位，子串下标回到开头，并开始一趟新的比较。这样说来可能晦涩难懂，下面将通过一个例子演示这个过程，假设主串为 "babaabd"，子串为 "abad"

*初始过程*

```
 b a b a a b d
 |
 a b a d
```

*'b' 和 'a' 不相同，开始下一趟匹配*

```
 b a b a a b d
   |
   a b a d
```

*主串和子串对应字符相同，此趟匹配继续*

<p id="some"></p>
---

```
 b a b a a b d
   | |
   a b a d
   
 b a b a a b d
   | | |
   a b a d
 
 b a b a a b d
   | | | |
   a b a d
```

---

*'a' 和 'd' 不相同，开始下一趟匹配*

```
 b a b a a b d
     |
     a b a d
```

*'b' 和 'a' 不相同，开始下一趟匹配*

```
 b a b a a b d
       |
       a b a d
```

*主串和子串对应字符相同，此趟匹配继续*

```
 b a b a a b d
       | |
       a b a d
```

*'a' 和 'b' 不相同，到达主串末端，字符串匹配结束*

简单字符串匹配算法的时间复杂度为 O(m*n)，同时该算法符合常理、简单易懂。但是，该算法存在一些问题，在上图的[某一趟匹配中](#some)(如下列出)，此时 'a' != 'd'，所以应当开始下一趟匹配，如下图的 next 所示。但 KMP 算法有所不同，在KMP算法的下一趟匹配中，子串直接"前移"了两个字符，从而避免了某些不必要的字符比较。KMP 算法之所以能够实现这种"跳跃"，得益于部分匹配表的支持，下一小节将着重介绍部分匹配表。

```java
// 'a'!= b
 b a b a a b d
   | | | |
   a b a d
// next
 b a b a a b d
     | 
     a b a d
// KMP algorithm's next
 b a b a a b d
       | 
       a b a d
```



### 部分匹配表

假设主串为 "bacbababaabcbab"，子串为 "abababca" 

### 原理介绍

### 代码实现

### 代码优化

## 后记