---
layout:     post
title:      "LeetCode —— 质数排列（素数筛与阶乘计算擦出的火花）"
subtitle:   " \"写在新学期伊始\""
date:       2019-09-02 12:00:00
author:     "小兴安岭"
header-img: "img/nanjingbg.jpg"
catalog: true
tags:
    - 算法
    - 回溯
---

> “It's an extraordinary thing to meet someone who you can bare your soul to.”
> 

## 前言

最近每天只要有空都会写一两道算法题，一来可以为之后找工作做准备，二来能够培养一种冷静思考问题的能力，细想起来，很多问题的解决办法其实具有一定的相通性。

随着题目数量的积累，我发现，如果只是一昧地注重刷题数量，每天机械地做题，不去总结碰到的问题，不形成相应的解题思路，最终碰到没有见过的题还是不会，并且最终结果会演化为「为了做题而做题」。

我想总结一下自己在碰到不会的题时是如何思考的，以及正确解法的具体思路，从而避免「为了做题而做题」的情况出现。

最近在做动态规划相关的题目，碰到了两次「回溯」相关的题目，第一次([LeetCode 638: 大礼包](https://leetcode-cn.com/problems/shopping-offers/))因为使用暴力 dfs 做出来了题目，所以就没有在意性能更好的解法。直到在第二次碰到类似的题时([LeetCode 691: 贴纸拼词](https://leetcode-cn.com/problems/stickers-to-spell-word/))没有想到合适的方法，于是我想从头了解一下「回溯法」到底是何方神圣。	

## 回溯

从 [维基百科](https://en.wikipedia.org/wiki/Backtracking) 中可以了解到，「回溯法」不是某一种具体的算法，它指的是在解决某些约束满足问题时，我们尝试构造出所有候选解，并且，当我们发现该候选解的某一部分不满足正确条件时放弃搜索这一候选解以及这部分拓展出的子候选解，从而去构造其他的候选解。

这句话听起来很拗口，但它的实际意思是，我们分步地列出满足约束条件的所有解（通常是使用递归的方式），在列出解的过程中（即某一次递归），如果我们发现现有的分步答案不能有效地得到正确解（即这趟递归在现有条件下已经没有必要继续进行），我们会回退到上一步甚至上几步去寻找其他解。

递归可以帮我们暴力列举出所有解，我们可以找出所有解中的可行解。「回溯」的作用在于我们不必找出所有解，当我们在找某一个解的过程中，如果我们已经确定没有继续往下找的必要，那么我们可以回退到上一步甚至上几步，这样可以避免不必要的查找，从而节省时间。

关键在于，每个问题的约束条件是不一样的，我们怎么知道在当前情况下是否还有往下找的必要呢？以及，如果要回溯，应该往上回退多少步合适呢？这些关键问题目前我也无法给出具体的解决方案，因此我想通过上文的两个例子（[LeetCode 638: 大礼包](https://leetcode-cn.com/problems/shopping-offers/) 和 [LeetCode 691: 贴纸拼词](https://leetcode-cn.com/problems/stickers-to-spell-word/)）简单地了解一下「回溯法」的具体应用。

### LeetCode 648 大礼包

> 在LeetCode商店中， 有许多在售的物品。
>
> 然而，也有一些大礼包，每个大礼包以优惠的价格捆绑销售一组物品。
>
> 现给定每个物品的价格，每个大礼包包含物品的清单，以及待购物品清单。请输出确切完成待购清单的最低花费。
>
> 每个大礼包的由一个数组中的一组数据描述，最后一个数字代表大礼包的价格，其他数字分别表示内含的其他种类物品的数量。
>
> 任意大礼包可无限次购买。

示例 1：

```
输入: [2,5], [[3,0,5],[1,2,10]], [3,2]
输出: 14
解释: 
有A和B两种物品，价格分别为¥2和¥5。
大礼包1，你可以以¥5的价格购买3A和0B。
大礼包2， 你可以以¥10的价格购买1A和2B。
你需要购买3个A和2个B， 所以你付了¥10购买了1A和2B（大礼包2），以及¥4购买2A。
```

示例 2：

```
输入: [2,3,4], [[1,1,0,4],[2,2,1,9]], [1,2,1]
输出: 11
解释: 
A，B，C的价格分别为¥2，¥3，¥4.
你可以用¥4购买1A和1B，也可以用¥9购买2A，2B和1C。
你需要买1A，2B和1C，所以你付了¥4买了1A和1B（大礼包1），以及¥3购买1B， ¥4购买1C。
你不可以购买超出待购清单的物品，尽管购买大礼包2更加便宜。
```

说明：

```
最多6种物品， 100种大礼包。
每种物品，你最多只需要购买6个。
你不可以购买超出待购清单的物品，即使更便宜。
```

#### 初始思路

我的思路是将单种物品也看成是大礼包，然后递归地列举出每一种购买情况，统计出最少花费。

例如，示例 1 中 [[3,0,5],[1,2,10]] 代表大礼包 1 和大礼包 2：

- 大礼包 1，你可以以 ¥5 的价格购买 3A 和 0B
- 大礼包 2， 你可以以 ¥10 的价格购买 1A 和 2B

由于 A 与 B 的单价分别为￥2 和 ￥5，所以我们将两者也看做大礼包，这样总的大礼包就是 [[3,0,5],[1,2,10],[1,0,2],[0,1,5]]

- 大礼包 1，你可以以 ¥5 的价格购买 3A 和 0B
- 大礼包 2， 你可以以 ¥10 的价格购买 1A 和 2B
- **大礼包 3，你可以以 ¥2 的价格购买 1A 和 0B**
- **大礼包 4，你可以以 ¥5 的价格购买 0A 和 1B**

最后，我们通过深度遍历找出每一种情况并统计最低花费，具体代码如下：

```
class Solution {
    //最终结果
    int min = Integer.MAX_VALUE;
    public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
        //将单个商品也看成大礼包
        for(int i=0;i<price.size();i++){
            List<Integer> list = new ArrayList<>();
            for(int j=0;j<price.size();j++){
                if(j==i){
                    list.add(1);
                }else{
                    list.add(0);
                }
            }
            list.add(price.get(i));
            special.add(list);
        }
        //开始调用递归函数
        findNext(needs,special,new ArrayList<Integer>(),0);
        return min;
    }
    //needs: 各类物品还需要的数量
    //special: 大礼包
    //tmp: 已经购买的大礼包
    //start: dfs开始的下标
    public void findNext(List<Integer> needs,List<List<Integer>> special,List<Integer> tmp,int start){
        boolean isEqual = true;
        for(int i=0;i<needs.size();i++){
            //如果买的商品已经多出了需求，则返回上一步
            if(needs.get(i)<0){
                return;
            }
        }
        for(int i=0;i<needs.size();i++){
            if(needs.get(i)>0){
                //如果买的商品还没有多出需求，继续往下查找
                isEqual = false;
                break;
            }
        }
        if(isEqual){
            //刚好满足需求，则进行统计最小花费，统计完之后返回上一步
            int sum = 0;
            for(int i:tmp)
                sum+=i;
            min = Math.min(sum,min);
            return;
        }
        for(int i=start;i<special.size();i++){
            // i 表示当前购买大礼包的编号
            List<Integer> choose = special.get(i);
            //更新还需要购买的商品数，将当前购买的大礼包加入 list
            for(int j=0;j<needs.size();j++){
                needs.set(j,needs.get(j)-choose.get(j));
            }
            tmp.add(choose.get(choose.size()-1));
            //继续递归
            findNext(needs,special,tmp,i);
            //结束递归后将还需要购买的商品数恢复为递归之前的状态，移除刚刚加入的大礼包
            for(int j=0;j<needs.size();j++){
                needs.set(j,needs.get(j)+choose.get(j));
            }
            tmp.remove(tmp.size()-1);
        }
    }
}
```

上面这段代码就是毫无优化的深度遍历，我们可以提交之后看下性能如何：

![](http://ww1.sinaimg.cn/large/7a776fe5ly1g67m63dfxsj20j70ajq3b.jpg)

这个执行时间简直不敢恭维。但是！当时因为沉浸在做出题目的喜悦之中，因此哪里还有心思要继续优化呢？

这是「回溯法」吗？

可以说是，因为我们做到了如下几点：

- 在满足约束的条件下尝试列出所有候选解
- 当我们发现当前购买的商品数已经大于所需商品数（不满足约束）时，我们停止继续往下递归，并且回退到上一层递归函数

总觉得哪里不对却又说不上来，大概是它的执行时间没法让我满意吧。于是我决定参考一下其他人的解法。

看了看题目的评论区和题解，大家都在清一色地讨论「回溯法」+「剪枝」，又有一个新的词语出现了。什么是「剪枝」呢？

顾名思义，「剪枝」就是剪去“枝条”，不过这里的“枝条”可不是真正的枝条，而是深度遍历（dfs）和广度遍历（bfs）产生的搜索树的枝条。所以，「剪枝」就是在搜索算法（一般是 dfs 和 bfs）中，通过某种判断，从而避免一些不必要的遍历过程。形象地讲，就像剪掉了搜索树的某些“枝条”一样，所以叫做「剪枝」。

跟「回溯」类似，这也是一个很抽象的问题，「剪枝」的关键在于判断哪些“枝条”应当舍弃，哪些应当保留。而题解里提到的「回溯法」+「剪枝」，我想就是通过「剪枝」判断这一步没有必要往下继续“走”，从而返回到上一步继续查找最优解。

#### 优化

之前我的思路是将“单个物品”也看成“大礼包”，然后进行暴力查找。这样做的目的是将“购买的所有物品中包含不购买大礼包”的情况考虑在内，但是这样做会增加查找时间。我们能不能将“大礼包”和“单个物品”分开考虑呢？

我们采用如下方法对暴力 dfs 进行优化：

- 我们只对“大礼包”进行 dfs ，**不把“单个物品”看成大礼包**
- 在某次递归中，我们计算出当前需求中“单独购买每种物品”所需的花费，如果购买某个“大礼包”的花费大于“单独购买每种物品”所需的花费，那么不购买“大礼包”，单独购买每种物品，并统计最小花费（「剪枝」）
- 在某次递归中，如果某个“大礼包”包含的某种物品大于该物品需要购买的数量，我们同样不购买该“大礼包”，并且单独购买剩余的物品，并统计最小花费（「剪枝」）

最终的代码如下：

其中，direct 代表当前需求下直接购买每种物品所需要的花费，如果购买某个大礼包的花费 > direct 或者某个大礼包包含的物品大于当前需求，那么我们就进行「剪枝」，并且「回溯」到上一步，考虑其他的“大礼包”。

```
class Solution {
    //最终结果
    int min = Integer.MAX_VALUE;
    public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
        //开始调用递归函数
        findNext(price,needs,special,new ArrayList<Integer>(),0);
        return min;
    }
    //price: 各类物品的单价
    //needs: 各类物品还需要的数量
    //special: 大礼包
    //tmp: 已经购买的大礼包
    //start: dfs开始的下标
    public void findNext(List<Integer> price,List<Integer> needs,List<List<Integer>> special,List<Integer> tmp,int start){
        // 统计一下当前需求如果直接购买需要多少钱
        int direct = directPurchase(needs,price);
        boolean isEqual = true;
        for(int i=0;i<needs.size();i++){
            if(needs.get(i)>0){
                //如果买的商品还没有多出需求，继续往下查找
                isEqual = false;
                break;
            }
        }
        if(isEqual){
            //刚好满足需求，则进行统计最小花费，统计完之后返回上一步
            int sum = 0;
            for(int i:tmp)
                sum+=i;
            min = Math.min(sum,min);
            return;
        }
        for(int i=start;i<special.size();i++){ 
            // i 表示当前购买大礼包的编号
            List<Integer> choose = special.get(i);
            if(choose.get(choose.size()-1)>direct||!canChoose(needs,choose)){
                int sum = 0;
                for(int j:tmp)
                    sum+=j;
                min = Math.min(min,sum+direct);
                continue;
            }
            //更新还需要购买的商品数，将当前购买的大礼包加入 list
            for(int j=0;j<needs.size();j++){
                needs.set(j,needs.get(j)-choose.get(j));
            }
            tmp.add(choose.get(choose.size()-1));
            //继续递归
            findNext(price,needs,special,tmp,i);
            //结束递归后将还需要购买的商品数恢复为递归之前的状态，移除刚刚加入的大礼包
            for(int j=0;j<needs.size();j++){
                needs.set(j,needs.get(j)+choose.get(j));
            }
            tmp.remove(tmp.size()-1);
        }
    }
    // 直接购买needs所需的物品的花费（不购买大礼包）
    public int directPurchase(List<Integer> needs,List<Integer> price){
        int res = 0;
        for(int i=0;i<needs.size();i++){
            res+=needs.get(i)*price.get(i);
        }
        return res;
    }
    // 判断这个大礼包包含的物品是否超出了当前需求
    public boolean canChoose(List<Integer> needs,List<Integer> choose){
        for(int i=0;i<needs.size();i++){
            if(needs.get(i)<choose.get(i)){
                return false;
            }
        }
        return true;
    }
}
```

执行时间果然得到了优化：

![](http://ww1.sinaimg.cn/large/7a776fe5ly1g68qhqdelij20ev03x3yl.jpg)

#### 简洁代码

回过头来看，我的代码显得十分臃肿，问题在于，需要统计最小花费的情况有两处，我将其分开讨论。在查看了其他人写的代码后，我决定对上述代码做一些修改，让它更加简洁：

- 递归的思路还是不变，每次递归就是找满足当前需求 needs 的各种情况
- 在每次递归中，我们返回满足当前需求的最小花费（dfs 返回 int 而不是 void）
- 在每次递归中，我们首先算出“**直接购买**”当前需求的各种物品所需的花费，然后，我们过滤掉“超出当前需求”的大礼包，最后，我们取“直接购买各种物品”和“购买大礼包”这两种情况花费的最小值。在考虑完所有“大礼包”后，我们返回最小值（如果所有大礼包都不能买，最小值就是“直接购买各种物品”所需的花费）

代码如下：

是不是简洁了许多呢？

```
class Solution {
    public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
        return dfs(price,special,needs,0);
    }
    public int dfs(List<Integer> price,List<List<Integer>> special, List<Integer> needs,int start){
        int min = directPurchase(needs,price);
        for(int i=start;i<special.size();i++){
            List<Integer> choose = special.get(i);
            List<Integer> tmp = new ArrayList<>();
            for(int j=0;j<needs.size();j++){
                if(choose.get(j)>needs.get(j)){
                    tmp = null;
                    break;
                }
                tmp.add(needs.get(j)-choose.get(j));
            }
            if(tmp!=null){
                min = Math.min(min,choose.get(choose.size()-1)+dfs(price,special,tmp,i));
            }
        }
        return min;
    }
    // 直接购买needs所需的物品的花费（不购买大礼包）
    public int directPurchase(List<Integer> needs,List<Integer> price){
        int res = 0;
        for(int i=0;i<needs.size();i++){
            res+=needs.get(i)*price.get(i);
        }
        return res;
    }
}
```

不仅代码变得简洁，执行时间也更短了

![](http://ww1.sinaimg.cn/large/7a776fe5ly1g68r2a4l8cj20j003tmx8.jpg)

### LeetCode 691 贴纸拼词

下面再来看另一道跟「回溯法」相关的题：

> 我们给出了 N 种不同类型的贴纸。每个贴纸上都有一个小写的英文单词。
>
> 你希望从自己的贴纸集合中裁剪单个字母并重新排列它们，从而拼写出给定的目标字符串 target。
>
> 如果你愿意的话，你可以不止一次地使用每一张贴纸，而且每一张贴纸的数量都是无限的。
>
> 拼出目标 target 所需的最小贴纸数量是多少？如果任务不可能，则返回 -1。

示例 1：

```
输入：
["with", "example", "science"], "thehat"

输出：
3

解释：
我们可以使用 2 个 "with" 贴纸，和 1 个 "example" 贴纸。
把贴纸上的字母剪下来并重新排列后，就可以形成目标 “thehat“ 了。
此外，这是形成目标字符串所需的最小贴纸数量
```

示例 2：

```
输入：
["notice", "possible"], "basicbasic"

输出：
-1

解释：
我们不能通过剪切给定贴纸的字母来形成目标“basicbasic”。
```

提示：

```
stickers 长度范围是 [1, 50]。
stickers 由小写英文单词组成（不带撇号）。
target 的长度在 [1, 15] 范围内，由小写字母组成。
在所有的测试案例中，所有的单词都是从 1000 个最常见的美国英语单词中随机选取的，目标是两个随机单词的串联。
时间限制可能比平时更具挑战性。预计 50 个贴纸的测试案例平均可在35ms内解决。
```

#### 思路

这题其实和 “大礼包” 很像。例如，示例 1 中的 "thehat" 需要两个 't'，两个 'h'，一个 'e' 和一个 'a'。而待选的贴纸包含如下内容：

| sticker | t    | h    | e    | a    |
| ------- | ---- | ---- | ---- | ---- |
| with    | 1    | 1    | 0    | 0    |
| example | 0    | 0    | 1    | 1    |
| science | 0    | 0    | 2    | 0    |

也就是说，可以从这些贴纸中选择包含我们所需要字符的贴纸，每张贴纸可以无限选择。

这里，我们需要引入一种新的做法：「备忘录」，用于帮助「剪枝」

- 我们通过 dfs 列举出每一种情况

- 我们在「备忘录」（hashmap）中记录构成某个单词 target 所需要的最小贴纸数量

- 在每次递归中，我们查询当前单词 target 是否在「备忘录」中出现。如果出现，我们直接返回该值即可

- 由于「备忘录」的存在，我们在考虑当前单词 target 时，如果某个贴纸不包含单词的首字母，我们直接「回溯」到上一步去考虑其他的贴纸。这样不会跳过正确答案，因为我们在下一次递归时会从第一个贴纸开始，而「备忘录」的存在会帮助我们大大节省时间

具体代码如下：

```
class Solution {
    public int minStickers(String[] stickers, String target) {
    	// 通过int[26]记录sticker出现字符的次数
        int len = stickers.length;
        int[][] map = new int[len][26];
        for(int i=0;i<len;i++){
            String cur = stickers[i];
            for(int j=0;j<cur.length();j++){
                map[i][cur.charAt(j)-'a']++;
            }
        }
        // 备忘录
        Map<String,Integer> dp = new HashMap<>();
        dp.put("",0);
        return dfs(map,target,dp);
    }
    public int dfs(int[][] map,String curTarget,Map<String,Integer> dp){
    	// 备忘录中已经出现直接返回备忘录中存的值
        if(dp.get(curTarget)!=null){
            return dp.get(curTarget);
        }
        int min = Integer.MAX_VALUE;
        for(int i=0;i<map.length;i++){
            if(map[i][curTarget.charAt(0)-'a']==0){
                continue;//剪枝，如果这个贴纸不包含当前target的第一个字符，则考虑下一个贴纸
            }
            // 计算选择该贴纸后剩下的单词
            int[] tmp = new int[26];
            for(int j=0;j<curTarget.length();j++){
                tmp[curTarget.charAt(j)-'a']++;
            }
            for(int j=0;j<26;j++){
                if(tmp[j]!=0&&map[i][j]!=0){
                    tmp[j] -= map[i][j];
                }
            }
            StringBuilder sb = new StringBuilder();
            for(int j=0;j<26;j++){
                for(int k=0;k<tmp[j];k++){
                    sb.append((char)('a'+j));
                }
            }
            // 选择该贴纸后，剩下的字符继续递归
            int next = dfs(map,sb.toString(),dp);
            // 取最小值
            if(next!=-1){
                min = Math.min(min,next+1);
            }
        }
        // -1 表示没找到，将其加入备忘录
        dp.put(curTarget,(min==Integer.MAX_VALUE)? -1 : min);
        return dp.get(curTarget);
    }
}
```

在代码中可以看到，我们把「备忘录」命名为 dp，这是因为这种做法和「动态规划」有些类似。但我更加偏向于「备忘录」的说法，也就是带有记忆功能的 dfs。

## 总结

本文从「回溯法」入手，简要分析了两个例子。从中我们可以分析出一些结论：

- 「回溯法」和「剪枝」都是一种思想，「剪枝」发生时往往会「回溯」到上一步。「回溯法」是解决问题的思路，而「剪枝」则是确定在何处进行「回溯」。
- 面对不同问题，「剪枝」的条件是千差万别的。像 [LeetCode 691: 贴纸拼词](https://leetcode-cn.com/problems/stickers-to-spell-word/) 就使用了「备忘录」的手段协助「剪枝」。类似于「动态规划」，如何操作需要我们具体问题具体分析，这其中的方法仅靠上面两个例子肯定无法完全概括，这里也将其留作一个后续工作。



## 后记

这篇博客原本打算在 8.7 坐车回家时完成，但最近经历了很多事情，在家的时间里也没有拿出时间学习。一拖再拖，今天已经是8.23了。

不管怎样，前面的路还是要走。

另外，注意身体啊，心理已经很变态了，身体再不健康怎么能行呢？

*2019.8.23 12:37*

