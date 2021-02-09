---
title: Educational Codeforces Round 92 (Rated for Div. 2) B、C题解
author: JoyDee
categories: [Online Judge, Codeforces]
tags: [算法-暴力, 思维-性质观察, 算法-贪心]
date: 2020-08-03 16:54:00 +0800
math: true
---

~~TAT 第一场codeforces~~

# B. Array Walk #暴力 #贪心

## [题目链接](https://codeforces.com/contest/1389/problem/B)

## 题意：

有$a1, a2, ..., an$ 个格子(每个格子有各自分数)，最初为1号格(初始分数为$a1$)，支持两种走法(经过的格子分数会相应累加)，只能走$k$步：①向右走。②向左走，但是**每一次向左操作走完一格后不能再连续地向左移动**，允许向左走的操作次数为$z$。现要求你走完k次后获得的最大分数。

## 分析：

参考了官方题解，假定我们有$t$次移动是向左的，那么剩下$k-t$次向右，我们知道遍历的格子在$[1, 1+k-2t]$的，即最终停留的位置一定为$1+k-2t$号格子(因为每一次的向左移动之后需要进行一次向右走)。

为保证解尽可能大，一定是**选择分数最大 的 相邻格子 进行重复的折返**。于是我们可以尝试着暴力枚举向左次数$t$(即暴力枚举最终停留位置)并一路向右遍历将分数累加(暂不包括折返所得)，当然向右遍历的过程中，还要将当前路途中出现的 分数最大 的相邻格子 进行记录。直到将$t$枚举完，最优解即得。

```c++
#include <algorithm>
#include <cstdio>
using namespace std;
typedef long long ll;
const int MAXN = 1e5 + 5;
int a[MAXN];
int main(){
    int Q; scanf("%d", &Q);
    while(Q--){
        int n, k, z; scanf("%d%d%d", &n, &k, &z);
        for (int i = 1; i <= n; i++) scanf("%d", &a[i]); 
        int ans = 0;
        //允许向左走，但每次向左走只能走一步，意味着要折返，考虑到分数越高越好，当然是要选择最大的相邻 分数对 进行重复的折返
        for (int t = 0; t <= z; t++){ //“向左”操作t次 == 折返t次 (当t为0说明一路向右)
            int pos = 1 + k - t * 2; //pos代表最终到达的位置，"1"代表 起始位置为1，折返t次就消耗t*2次操作
            if(pos < 1) continue;
            int mx = 0, sum = 0;
            for (int i = 1; i <= pos; i++){ //计算分数
                if(i < n - 1) 
                    mx = max(mx, a[i] + a[i + 1]);//选择最大的相邻分数对
                sum += a[i];//同时不断累加成绩(但注意，此时是累加前进时获得分数，没加上折返时获得分数)
            }
            ans = max(ans, sum + mx * t); 
        }
        printf("%d\n", ans);
    }
    return 0;
}

```

# C. Good String #暴力 #性质观察

## [题目链接](https://codeforces.com/contest/1389/problem/C)

## 题意:

给定由数字$0-9$组成的串$s = t_1t_2...t_n$，现要求最少从$s$中删除几个字符，使得新串满足$t_2...t_{n-1}t_{n}t_1 == t_{n}t_1t_2...t_{n-2}t{n-1}$

## 分析：

递推一下条件，可以得到$t_1 = t_3 = ... = t_{2k-1}$ 以及$t_2 = t_4 = ... = t_{2k}$，偶/奇数位置的字符各自相等，故满足$good string$ 当且仅当 该串的字符种数不超过2种。(eg: $23 23 23 23 ... 23$)

由于串仅有$0-9$种字符，于是我们枚举$[00, 99]$数字组合，统计每一种组合在原串中 或连续 或间断 的出现次数，并找出最大次数$A$，**利用奇偶位置枚举数字组合的方式值得学习**

然而这样交上去仍然会wa，参考了`@farer_yyh`的思路，你还需要额外地统计$0-9$在原串出现次数(即考虑删除后的新串只有一种字符)，找出最大次数$B$，最后取$A$和$B$的最大者，具体见代码

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>
using namespace std;
typedef long long ll;
const int MAXN = 2e5 + 5;
char str[MAXN];
int num[11]; //digit:0-9
int main() {
    int Q; scanf("%d", &Q);
    while (Q--) {
        memset(str, 0, sizeof(str));
        memset(num, 0, sizeof(num));
        scanf("%s", str);
        int len = strlen(str);
        if (len <= 2) {
            printf("0\n");
            continue;
        }
        for (int i = 0; i < len; i++)
            num[str[i] - '0']++; //统计该字符串每种字符出现次数
        int mymax = -1;
        for (int hi = 0; hi <= 9; hi++) {
            for (int lo = 0; lo <= 9; lo++) {
                int cnt = 0;
                for (int i = 0; i < len; i++) {
                    int c = str[i] - '0';
                    if (cnt & 1) {
                        if (c == hi)cnt++;
                    }
                    else {
                        if (c == lo)cnt++;
                    }
                }
                if (cnt & 1) cnt --; // (a,b)(a)的情况需要减1
                //但是对于(a, a)(a)无法统计出
                mymax = max(mymax, cnt); //统计出现次数最多的数字对(不一定连续)
            }
        }
        for (int i = 0; i <= 9; i++) //保证不漏下奇数长度且只有一种字符的good string
            mymax = max(mymax, num[i]);
        printf("%d\n", len - mymax);
    }
    return 0;
}
```

