---
title: 算法学习 数位DP
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-27 20:46:16
password:
summary:
tags:
	- 算法训练
	- C++
categories: 基础算法学习
---
# 数位DP

这一节来介绍一种特殊的DP方法，数位DP，一般数位DP的求解问题模板就是，当我们需要求解从l ~ r中所有数字存在的特征的时候所使用的一种方法。

一般的暴力形式是

```C++
for(int i = l; i <= r; i ++ )
{
    if(i)	ans ++;
}
```

可以知道我们就是通过这个东西来求解所有数字的特征。而我们数位DP就是寻找一种快捷的方式或者函数求解出来其中对于1 ~ n的数字的特征，然后通过求解count（r） - count（l）来求出我们需要的结果。

---

下面我们看一个题目

### 计数问题

![image-20211027205431892](C:\Users\86385\AppData\Roaming\Typora\typora-user-images\image-20211027205431892.png)

这是一个典型的数位DP问题，下面看看他的输入输出

![image-20211027205538736](C:\Users\86385\AppData\Roaming\Typora\typora-user-images\image-20211027205538736.png)

先介绍本题的解答思路， 首先我们可以将问题求解一个简单模型，就是从1~abcdefg里面的每个数字的位数和。假设此时我们需要求解在1~abcdefg里面第四位上面1的个数，我们可以将数字分为第四位左边以及第四位右边来看。

```题解
基本模型就是我们需要求出abc1efg这样模型的数字个数
①当前三位是0 ~ abc - 1时 对于efg无任何要求 就是前三位有abc种可能，后三位有000~999这1000种可能一共是（abc）* 1000种可能
②当前四位是abc1时
	（1）d < 1时 abc1efg > abc0efg -》 0
	（2）d = 1时 当efg有 000 - efg一共efg + 1种选择
	（3）d > 1时 efg有 1000 种选择
把所有的结果相加就是我们需要的结果
```



题解：

```C++
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

int a, b;

int get(vector<int> a, int l, int r)//求出vector里面一个段所代表的数字是多少
{
    int res = 0;
    for(int i = r; i >= l; i -- )
    {
        res = res * 10 + a[i];
    }
    return res;
}

int pow10(int n)//10的几次方
{
    int res = 1;
    while(n --) res *= 10;
    return res;
} 

int count(int n, int x)//n代表的是到哪个数字结束， i代表的是统计哪个数字
{
    if(!n)  return 0;
    
    vector<int> num;
    while(n)//把n存入vector数组当中
    {
        num.push_back(n % 10);
        n /= 10;
    }
    
    n = num.size();
    int res = 0;
    
    for(int i = n - 1 - !x; i >= 0; i -- )
    {
        if(i < n - 1)
        {
            res += get(num, i + 1, n - 1) * pow10(i);
            if(!x)  res -= pow10(i);
        }
        
        if(num[i] == x) res += get(num, 0, i - 1) + 1;
        if(num[i] > x)  res += pow10(i);
    }
    return res;
}

int main()
{
    while(cin >> a >> b && a)
    {
        if(a > b)   swap(a, b);
        
        for(int i = 0; i <= 9; i ++ )
        {
            cout << count(b, i) - count(a - 1, i) << ' ';
        }
        cout << endl;
    }
    return 0;
}
```

AC代码如上，所以在解决数位DP问题时，我们还是去想象如何把他转化成子问题来解决。

---