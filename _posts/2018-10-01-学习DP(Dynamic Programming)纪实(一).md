---
layout: post
title: 学习DP(Dynamic Programming)纪实(一) 
date: 2018-10-01
author: ygritte
tags:
    -dp
---
# 学习DP(Dynamic Programming)纪实(一) #
如果真要谈算法，平时做的OJ上的题目，也只有DP可以算得上算法了，但是由于DP的难以理解一直未能入门，临近毕业，找工作也排上了日程，刷题是必不可少的，借此找工作机会的契机，逼一逼自己，要努力啃下了这块硬骨头。

----------

先看DP入门的求第n(n从0算起)项Fibonacci数列的解法。
# 第一种解法 #
```
    int fibonacci(int n) {
        if (n < 2) {
            return n;
        }
        return fibonacci(n-1)+fibonacci(n - 2);
    }
```
其实，这样效率是很低的，以算第5个斐波那契数为例，整个计算过程分解成树形如下图所示。
```
           			     f(5)
                     /           \
                 f(4)     +      f(3)
               /      \         /      \
             f(3) +  f(2)      f(2)  +  f(1)
             / \     /   \     / \       |
        f(2) + f(1) f(1)+f(0) f(1)+f(0)+f(1)
         /  \   |    |    |    |    |    | 
      f(1)+f(0)+f(1) f(1)+f(0) f(1)+f(0)+f(1)
        1 + 0  +  1 +  1  +  0 +  1 + 0  + 1 
```
  最终算出fibonacci(5),即第5项为1+0+1+1+0+1+0+1=5。可以看出树中大部分都是在重复计算，比如f(5)下的左子树的f(4)已经计算了f(3)的值，右子树的f(3)的计算完全没必要。正是因为这种不断重复的计算使得这种递归效率比较底下。优化思路是**：如果能对左边子树的计算结果存起来，右子树不用再次计算，从而节省这些计算时间**。下面是优化的代码。
```
long[] mem = new long[n+1];
int fibonacci(int n) {
	if (mem[n] != 0) {
            return mem[n];
        }
    if (n < 2) {
        return n;
   }
   return mem[n]=fibonacci(n-1)+fibonacci(n - 2);
}
```

```
           			     f(5)
                     /           \
                 f(4)     +      f(3)=2
               /      \            |
             f(3)  + f(2)=1        |
             / \       |		   |
        f(2) + f(1)=1  |           |
         /  \    |     |           |
      f(1)+ f(0) |     |           |
       1  +  0 + 1  +  1    +      2
```
从树形结构可以看出来不必要的计算被剪掉了，真正的只计算了必要n。这种优化方式，是用空间换时间，我们开辟了一个n+1的数组，记忆已存储到的计算结果。
这种记忆化搜索正是实现DP的[*memoization*](https://en.wikipedia.org/wiki/Memoization)方式，它对应着解决DP问题的**自顶向下**(*Top Down*)的解法,**抛出完整的问题，完整的问题不断被分解成可以解决的子问题，最终子问题都得到解决，完整的问题也被解决。**记得与单词***memorization***区分,在学习过程中，我发现很多人都没解释这点。维基百科有以下解释

 *The term "memoization" was coined by Donald Michie in 1968[3] and is derived from the Latin word "memorandum" ("to be remembered"), usually truncated as "memo" in American English, and thus carries the meaning of "turning [the results of] a function into something to be remembered." While "memoization" might be confused with "memorization" (because they are etymological cognates), "memoization" has a specialized meaning in computing.*

# 第二种解法 #
下面用另一种非递归方式解决fibonacci数列问题。


观察到fibonacci的求解方程为 ***f(n)=f(n-1)+f(n-2)***，f(n)含义为第n个数的fibonacci值。
```   
int fibonacci(int n) {
     int[] f = new int[n + 1];
     f[0]=0;
     f[1]=1;
     for (int i = 2; i <= n; i++) {
         f[i] = f[i - 1] + f[i - 2];
     }
     return f[n];
}
```
可以看出来，我们可以口算出前两项的值，用这前两项就能推算出后面的值，只用了一个循环，效率比前面的递归方式高多了。


上文提到的***f(n)=f(n-1)+f(n-2)***，就可以称为**状态转移方程**(*state transition*)，名字听起来很学究气，但是学习要理解为重，对概念不可死记。求解一个dp问题的最关键之处就是能找出其隐含的状态转移方程，写出来它，伪代码也就呼之欲出了。

这种非递归的解法对应求解dp问题的**列表法***([tabulation](https://en.wiktionary.org/wiki/tabulation))*，它对应着**自底向上**(*Bottom Up*)的解法，**先把子问题都求解出来，最后得到完整问题的答案**。

对空间进一步优化，观察到我们只需要保留当前n的前两个fibonacci值，我们没必要开辟一个n+1大的数组，只需要要两个变量即可，空间复杂度从**O(n)**降到**O(1)**

```
static int fibonacci(int n) {
    int font = 1, end = 0, ans =0;
    for (int i = 2; i <= n; i++) {
        ans = font + end;
        end = font;
        font = ans;
    }
    return ans;
}
```

# 总结 #
这里总结的是我学习DP的入门问题的启蒙问题，求解fibonacci值。但是由于fibonacci问题过于简单，对DP中最重要的**状态转移方程**(*state transition*)中的**状态**都感觉不到，下一节，会总结一个复杂一点的DP问题，真正感受DP的魅力。
