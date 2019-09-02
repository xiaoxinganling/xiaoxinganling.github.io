---
layout:     post
title:      "LeetCode —— 质数排列（素数筛与阶乘计算擦出的火花）"
subtitle:   " \"写在新学期伊始\""
date:       2019-09-02 12:00:00
author:     "小兴安岭"
header-img: "img/lake.jpg"
catalog: true
tags:
    - 算法
    - 优化
    - 质数
    - 
---

> “Sometimes a problem is just an opportunity in disguise.”
> 
<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>
## 前言
最近都有参与周赛，可是成绩一直都不是很理想。上周在打周赛时被第一题卡了很久，细想起来，这一题竟然蕴含了两个我们经常会碰到，但却很容易忽视的知识点。

- 寻找质数
- 阶乘计算

正如标题所说，我想通过这道题讲一下这两个知识点。

## 初识 —— 质数排列
下面我们来看一下上文提及的这道题（[质数排列](https://leetcode-cn.com/problems/prime-arrangements/submissions/)）：

>请你帮忙给从 1 到 n 的数设计排列方案，使得所有的「质数」都应该被放在「质数索引」（索引从 1 开始）上；你需要返回可能的方案总数。
让我们一起来回顾一下「质数」：质数一定是大于 1 的，并且不能用两个小于它的正整数的乘积来表示。
由于答案可能会很大，所以请你返回答案 模 mod 10^9 + 7 之后的结果即可。

示例 1：

```
输入：n = 5
输出：12
解释：举个例子，[1,2,5,4,3] 是一个有效的排列，但 [5,2,3,4,1] 不是，因为在第二种情况里质数 5 被错误地放在索引为 1 的位置上。
```

示例 2：

```
输入：n = 100
输出：682289015
```

提示：

```
1 <= n <= 100
```

乍一看，这是一道很简单的题，首先我们找出小于等于 n 的所有质数 num，然后我们计算 num 的全排列 * (n-num) 的全排列，最后我们再返回答案。于是我们可能会有如下代码：

```java
class Solution {
    public int cal = 1000000007;
    public int numPrimeArrangements(int n) {
        int num = getPrimeNum(n);
        return (computeA(num)*computeA(n-num))%cal;
    }
    public int getPrimeNum(int n){
        int res = 0;
        for(int i=2;i<=n;i++){
            boolean isPrime = true;
            for(int j=2;j*j<=i;j++){
                if(i%j==0){
                    isPrime = false;
                    break;
                }
            }
            if(isPrime)
                res++;
        }
        return res;
    }
    public int computeA(int i){
        int res = 1;
        while(i>1){
            res = res * i;
            i--;
        }
        return res;
    }
}
```

这段代码有一个问题，在计算阶乘时会产生溢出，从而导致结果不正确。因此我们再回到题干：

> 由于答案可能会很大，所以请你返回答案 模 mod 10^9 + 7 之后的结果即可。

## 阶乘计算正确的打开方式

题目也提到了答案可能会很大，所以需要返回 result % (10^9+7) 即可。问题在于我们是累乘完之后再取模，这样很可能会导致溢出。

能不能在计算的过程中取模，例如，超过 10^9+7 就取模，再继续之后的运算呢？听起来有点不可思议，但其实这种做法是可行的。

我们假设 a1, a2, a3 ...... an 是一系列相乘的数，而 m 是取余所除的数，最后的结果为 result：


$$
result = (a_1 \times a_2 \times a_3 ...\times a_n)\, \%\, m
$$



假设乘法进行到 ai 时，前面的结果已经大于 m，式子转换为如下所示（为了方便展示我们把 % 写成 ÷）:

$$
result = \frac{(a_1 \times a_2 \times a_3 ... \times a_i)\times a_{i+1} \times a_{i+2} ... \times a_n}{m}
$$

因为括号里的值肯定大于 m，所以又可以写成如下所示，其中 y 是取模后的结果：

$$
result = \frac{((x \times m) + y)\times a_{i+1} \times a_{i+2} ... \times a_n}{m}
$$

拆开之后就是就是如下所示：

$$
result = \frac{(x \times m) \times a_{i+1} \times a_{i+2} ... \times a_n + (y \times a_{i+1} \times a_{i+2} ... \times a_n)}{m}
$$

加号的左边包含 m，所以肯定会被 m 除尽，在进行取模操作后的结果一定为 0，所以原式转换为：

$$
result = \frac{y \times a_{i+1} \times a_{i+2} ... \times a_n}{m}
$$

以此类推，继续往后乘时，只要当前结果大于m，我们就可以将当前结果替换为取余之后的值后继续计算，这样我们就可以把计算过程中用到的变量限制在某个区间范围内，从而杜绝溢出现象的产生。

因为本题中，取余所除的数 m = 10^9 + 7，而根据题目所给的条件，1<= n <= 100。所以在计算阶乘的过程中，中间变量的最大值不超过 (10^9+6) * 100 ≈ 10^10。又因为 int 类型的最大值为 2^31 - 1 = 2147483647 < 10^10，可能存在溢出。所以我们使用 long 类型防止溢出，这样代码就转化为如下所示：

```java
class Solution {
    public int cal = 1000000007;
    public int numPrimeArrangements(int n) {
        int num = getPrimeNum(n);
        return (int)((computeA(num)*computeA(n-num))%cal);
    }
    public int getPrimeNum(int n){
        int res = 0;
        for(int i=2;i<=n;i++){
            boolean isPrime = true;
            for(int j=2;j*j<=i;j++){
                if(i%j==0){
                    isPrime = false;
                    break;
                }
            }
            if(isPrime)
                res++;
        }
        return res;
    }
    public long computeA(int i){
        long res = 1;
        while(i>1){
            if(res>cal){
                res%=cal;
            }
            res*=i;
        }
        return res;
    }
}
```

## 寻找质数

这样考虑后，这题实际上就结束了。但本题还涉及到一个知识点 —— 统计 n 以内的质数数量，也就是寻找质数。

一个简单的做法是，对于当前的数 num，遍历从 2 开始到 (num-1) 所有数并判断其能否整除 num，如果能整除，证明 num 不是质数，否则是质数。

这个做法存在优化之处，上文采用的其实是优化之后的方法，即遍历的终点不需要到 num，只需要到 $\sqrt{num}$ 即可。因为，如果存在 x * y = num，且 x <= y，那么必定有 x <= $\sqrt{num}$, y >= $\sqrt{num}$。所以我们只需遍历到 $\sqrt{num}$ 就可以判断 num 到底是不是质数。

这种做法的最坏时间复杂度为 $O(n^{3/2})$，所以当 n很大时（百万级别），该方法消耗的时间比较多。

这里要介绍一种更快的方法（时间复杂度为 $O(n \log \log n)$） —— [sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes) （埃拉托斯特尼筛法，简称埃氏筛，也成素数筛）

## 素数筛

让我们回到质数的定义：除了自身和 1 以外不能被其他数整除。只要能被其他数整除，换一种说法，也就是能写成其他两个数的乘积，它就不是质数。

如果我们从 2 开始，依次筛除掉 $2\times x（x=2,3,4...num/2)$，筛除完 2 之后再考虑 3,4 ...，最后剩下的数就都是质数。

#### 初始想法

假设我们需要找到 17 （包括 17）以内的所有质数，我们可以尝试上面提到的方法。

```
初始状态(x代表删除)：
2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17

删除以 2 为因数的数：(2 * 2，2 * 3，2 * 4，2 * 5，2 * 6，2 * 7，2 * 8 肯定都不是质数)
2 3 x 5 x 7 x 9 xx 11 xx 13 xx 15 xx 17  

删除以 3 为因数的数：（3 * 2, 3 * 3, 3 * 4，3 * 5）
2 3 x 5 x 7 x x xx 11 xx 13 xx x xx 17

删除以 4 为因数的数：(4 * 2, 4 * 3, 4 * 4) 
2 3 x 5 x 7 x x xx 11 xx 13 xx x xx 17 (未改变)

删除以 5 为因数的数：(5 * 2, 5 * 3)
2 3 x 5 x 7 x x xx 11 xx 13 xx x xx 17 (未改变)

删除以 6 为因数的数：(6 * 2)
2 3 x 5 x 7 x x xx 11 xx 13 xx x xx 17 (未改变)

删除以 7 为因数的数：(7 * 2)
2 3 x 5 x 7 x x xx 11 xx 13 xx x xx 17 (未改变)

删除以 8 为因数的数：(8 * 2)
2 3 x 5 x 7 x x xx 11 xx 13 xx x xx 17 (未改变)
```

#### 剔除重复

我们发现，上述步骤存在很多无用功：

- 在「删除以 2 为因数的数」后，4 已经被标为非质数，所以「删除以 4 为因数的数」这一步包含在「删除以 2 为因数的数」中，「删除以 6 为因数的数」 同理。所以，如果碰到已经被标为非质数的数时，我们直接跳过。
- 在「删除以 2 为因数的数」时，我们考虑了 2 * 3，所以在考虑 3 时，我们不需要考虑 3 * 2。准确地说，我们应该直接从 3 * 3 开始。同理，在「删除以 5 为因数的数」时，我们应该直接从 5 * 5 开始，因为 5 * 4 、 5 * 3 和 5 * 2 已经被之前的情况考虑在内。（不过 5 * 5 已经大于 17，所以不用考虑 「删除以 5 为因数的数」）。

剔除重复后，步骤如下：

```
初始状态(x代表删除)：
2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17

删除以 2 为因数的数：(2 * 2，2 * 3，2 * 4，2 * 5，2 * 6，2 * 7，2 * 8)
2 3 x 5 x 7 x 9 xx 11 xx 13 xx 15 xx 17  

删除以 3 为因数的数：（3 * 3, 3 * 4，3 * 5）
2 3 x 5 x 7 x x xx 11 xx 13 xx x xx 17

删除以 5 为因数的数：(5 * 5 已经大于17，结束)
```

最后剩下的质数就是 2，3，5，7，11，13 和 17，这与正确结果保持一致。

所以素数筛的最终代码如下：

```java
public int countPrimes(int n) {
    boolean[] notPrime = new boolean[n+1];
    for(int i=2;i*i<=n;i++){
        if(!notPrime[i]){
            for(int j=i*i;j<=n;j+=i){
                notPrime[j] = true;
            }
        }
    }
    int res = 0;
    for(int i=2;i<=n;i++){
        if(!notPrime[i]){
            res++;
        }
    }
    return res;
}
```

由于本题中 n 最大为 100，所以是否使用素数筛对最终结果影响不大。当 n 的值越来越大时，素数筛的优势就愈发明显。

## 后记

本文主要阐述了如何寻找质数以及计算阶乘时存在的小技巧，这是 [质数排列](https://leetcode-cn.com/problems/prime-arrangements/submissions/) 这一题给我带来的思考与总结。为什么我要花费不少的时间来讲一道简单的题呢？一是我想趁机回顾一下「素数筛」（因为这也是典型的“空间换取时间”），另一个原因则是我在这上面摔了跟头，从而导致自己也没心情去做后面的题，对此我想指出自己的一些不足之处：

- 考虑问题时不够沉着冷静 —— 碰到问题时不是仔细思考并理清思路而是一头乱窜
- 不懂得灵活变通 —— 就像以前教我们考试的老师总是说碰到不会的题要跳过，其实遇到问题也是，如果一直死磕一个，最后的结果肯定不尽人意，如果适时放手，回过头来可能就是柳暗花明。

好在我很早就看到了这些不足，发现自己的弱小也算一件幸运的事。

—— 2019.9.2 21:21



