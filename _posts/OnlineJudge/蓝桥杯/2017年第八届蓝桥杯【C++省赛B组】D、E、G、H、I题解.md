可能因为我使用暴力思维比较少，这场感觉难度不低。

# B. 等差素数列 #暴力 #枚举

## 题意

类似：$7,37,67,97,127,157$ 这样完全由素数组成的等差数列，叫等差素数数列。
上边的数列公差为$30$，长度为$6$。现要你求长度为$10$的等差素数列，其**公差最小值**是多少？

## 分析

先将一定区间的整数筛出合数和质数来（这里用了欧拉筛法），接下来for循环中外层枚举这个公差，内层枚举等差素数数列的首项，只要长度满足10，即可退出。

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <map>
#include <queue>
#include <stack>
#include <string>
#include <cstring>
#include <vector>
#include <cmath>
#include <unordered_map>
#include <set>
#include <cmath>
#include <bitset>
using namespace std;
using ll = long long;
const int MAXN = 40;
const int INF = 0x3f3f3f3f;
unordered_map<ll, ll> prim; //质数表
unordered_map<ll, bool> vis; //判断该数是否为合数
int cnt, n = 50000;
void createPrim(){ //将1-50000的素数筛出来
    for(int i = 2; i <= n; i++){
        if(vis[i] == 0) prim[cnt++] = i;
        for(int j = 0; 1LL * i * prim[cnt] <= n && j <= cnt; j++){
            vis[1LL * i * prim[j]] = true;
            if(i % prim[j] == 0) break;
        }
    }
} //欧拉筛法
int findDiff(){
    for(int diff = 1; diff <= 10000; diff++){ //枚举公差
        for(int beg = 2; beg <= 10000; beg++){ //枚举起点
            int cur = beg, cnt = 1;
            if(vis[beg] == true) continue; //如果该点是合数，则不符合题意
            while(cnt < 10){
                if(vis[cur + diff] == false) cnt++; 
                else break; 
                cur += diff;
            }
            if(cnt == 10) return diff; //存在长度为10的等差素数列
        }
    }
    return -1;
}
int main(){
    //createPrim();
    //cout << findDiff() << endl;
    cout << 210 << endl; //答案为210
    return 0;
}
```

# C. 承压计算 #暴力

## 题意

金属材料被严格地堆放成金字塔形。其中的数字代表金属块的重量（计量单位较大），而最下一层的X代表30台极高精度的电子秤。假设每块原料的重量都十分精确地**平均**落在**下方的两个**金属块上，最后，所有的金属块的重量都严格精确地平分落在最底层的电子秤上。

工作人员发现，其中读数最小的电子秤的示数为：$2086458231$。现要你求出读数最大的电子秤的示数(要求整数)为多少？

## 分析

不要被这个三角形吓到，不妨将这个三角形贴在左侧，将其补全为一个矩形（空余部分用`0`填补），然后暴力地，从高处到低处，一个个统计所承受的重量。设最高层为第`0`层，则第`i`层下的第`j`个的承受重量$Ma[i][j]=Ma[i-1][j-1]/2+Ma[i-1][j]/2$。这里注意下边界情况，尤其是当`j`为`0`时，它只需要承受右上方（在原金字塔中）的金属块积累的重量，左上方无金属块。暴力统计完后，找出最底层的最小值重量（原因见下）和最大值重量

另外，你会疑问题目给出的$2086458231$用来干啥，实际上电子秤的读数不完全匹配金属块承受的实际重量，你需要利用最小的金属块承受的实际重量与其对应的读数之比值，来算出最大承受重量对应的读数。

```c++
7                            ....                                             
5 8                                                      
7 8 8             
9 2 7 2 
8 1 4 9 1 
8 1 8 8 4 1 
7 9 6 1 4 5 4 
5 6 5 5 6 9 5 6 
5 5 4 7 9 3 5 5 1 
7 5 7 9 7 4 7 3 3 1                                     
4 6 4 5 5 8 8 3 2 4 3 
1 1 3 3 1 6 6 5 5 4 4 2 
9 9 9 2 1 9 1 9 2 9 5 7 9 
4 3 3 7 7 9 3 6 1 3 8 8 3 7 
3 6 8 1 5 3 9 5 8 3 8 1 8 3 3 
8 3 2 3 3 5 5 8 5 4 2 8 6 7 6 9 
8 1 8 1 8 4 6 2 2 1 7 9 4 2 3 3 4 
2 8 4 2 2 9 9 2 8 3 4 9 6 3 9 4 6 9 
7 9 7 4 9 7 6 6 2 8 9 4 1 8 1 7 2 1 6 
9 2 8 6 4 2 7 9 5 4 1 2 5 1 7 3 9 8 3 3 
5 2 1 6 7 9 3 2 8 9 5 5 6 6 6 2 1 8 7 9 9 
6 7 1 8 8 7 5 3 6 5 4 7 3 4 6 7 8 1 3 2 7 4 
2 2 6 3 5 3 4 9 2 4 5 7 6 6 3 2 7 2 4 8 5 5 4 
7 4 4 5 8 3 3 8 1 8 6 3 2 1 6 2 6 4 6 3 8 2 9 6 
1 2 4 1 3 3 5 3 4 9 6 3 8 6 5 9 1 5 3 2 6 8 8 5 3 
2 2 7 9 3 3 2 8 6 9 8 4 4 9 5 8 2 6 3 4 8 4 9 3 8 8 
7 7 7 9 7 5 2 7 9 2 5 1 9 2 6 5 3 9 3 5 7 3 5 4 2 8 9 
7 7 6 6 8 7 5 5 8 2 4 7 7 4 7 2 6 9 2 1 8 2 9 8 5 7 3 6 
5 9 4 5 5 7 5 5 6 3 5 3 9 5 8 9 5 4 1 2 6 1 4 3 5 3 2 4 1 
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
```

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <map>
#include <queue>
#include <stack>
#include <string>
#include <cstring>
#include <vector>
#include <cmath>
#include <unordered_map>
#include <set>
#include <cmath>
#include <bitset>
using namespace std;
using ll = long long;
const int MAXN = 40;
const int INF = 0x3f3f3f3f;
double Ma[40][40];
int n = 30, m = 30;
int main(){
    for(int i = 0; i < n; i++){
        for(int j = 0; j < m; j++){
            scanf("%lf", &Ma[i][j]);
        }
    }
    for(int i = 1; i < n; i++){ //第0行跳过
        for(int j = 0; j < m; j++){ 
            if(j == 0) Ma[i][j] += (Ma[i - 1][j] * 1.0 / 2);
            else Ma[i][j] += ((Ma[i - 1][j - 1] + Ma[i - 1][j]) * 1.0 / 2);
        }
    }
    double mymax = -1, mymin = (double)0x3f3f3f3f;
    for(int j = 0; j < m; j++){ //对n-1行（从0计数的第29行）进行计算
        mymax = max(mymax, Ma[n - 1][j]);
        mymin = min(mymin, Ma[n - 1][j]);
    }
    double ans_1 = 2086458231 * 1.0 / mymin * mymax;
    long long ans_2 = ans_1;
    printf("%lld\n", ans_2); //答案要求取整
    //cout << 72665192664 << endl;
    return 0;
}


```

# D.方格分割 #深搜

## 题意

6x6的方格，沿着格子的边线剪开成两部分。要求这两部分的**形状**完全相同。下图中即为其中两种不同的分割方法。注意，旋转对称的属于同一种分割法。现要你求出可行的不同的分割方法。

![p1](https://gitee.com/j__strawhat/MyImages/raw/master/p1.png) ![p3](https://gitee.com/j__strawhat/MyImages/raw/master/p3.png)


## 分析

一开始我以为暴力枚举出整个方格的所有状态，但程序跑起来太慢了qaq。

参考了[@imagination_wdq思路](https://blog.csdn.net/qq_42391248/article/details/81320772)，他的思路是枚举**割痕的路径**，也就说我们需要关注方格**线**的交点。我们观察到6*6的格子，一共有7条竖线和7条横线，我们设第一条竖线与第一条横线的交点为$(1,1)$。首先我们知道题目中分割的两部分形状是关于中心对称的，我们将一条割痕分为两部分，一部分割痕路径确定下来，另一部分也就确定下来了（中心对称过去即能得到）。同时，正因为是中心对称，最初割的起点，一定是方格线交点$(3,3)$。我们于是从中心割点出发，DFS搜索路径（注意要判断每个割点是否在该路径下访问过），只要搜索到有一侧的割点在于边界，说明你成功将格子分割出两个中心对称图形了，于是便计入答案。

![image-20201014002857156](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201014002857156.png)

由于题目要求，分割出来的图形，再往另外三个方向旋转后得出来，仍属于同一种分割方法，所以要对答案除$4$。

```c++
#include <string>
#include <cstring>
#include <cstdio>
#include <iostream>
#include <stack>
#include <cmath>
#include <queue>
#include <map>
#include <vector>
#include <deque>
#include <algorithm>
#include <unordered_map>
using namespace std;
using ll = long long;
const int MAXN = 1e5 + 5;
bool vis[7][7];
int dx[] = {0, 1, 0, -1, 0};
int dy[] = {0, 0, -1, 0, 1};
ll ans = 0;
void DFS(int x, int y){
    if(x == 0 || x == 6 || y == 0 || y == 6){ //到达四个边界之一，说明已将图形分割出来
        ans++; return;
    }
    for(int t = 1; t <= 4; t++){
        int nx = x + dx[t], ny = y + dy[t];
        if(vis[nx][ny]) continue;
        vis[nx][ny] = vis[6 - nx][6 - ny] = true;
        DFS(nx, ny);
        vis[nx][ny] = vis[6 - nx][6 - ny] = false; 
    }
}
int main(){
    vis[3][3] = true;
    DFS(3, 3); //割痕中心点出发搜索
    printf("%lld\n", ans * 1LL / 4);
}

```

# H. 包子凑数 #完全背包 #不定方程

## 题意

包子铺有$N(\leq100)$种蒸笼，其中第$i$种蒸笼恰好能放$A_i(\leq100)$个包子。每种蒸笼都有**无限笼**。如果想买$X$个包子，包子大叔会选出若干笼包子，使得这若干笼**恰好**总共有$X$个。（也就说，取出来的蒸笼，不能有剩余，要全部拿出来）现你要求出一共有多少**种** **数字**是包子大叔凑不来的，若有无限个，则输出`INF`。

## 分析

> **斐蜀定理**：若$a,b$是整数,且$gcd(a,b)=d$，那么对于任意的整数$x,y$, $ax+by$都一定是$d$的倍数。

对于一个不定方程$ax+by=c$，

+ 若$a,b$互质（也就是$gcd(a,b)=1$），那么$x,y$一定有解，同时$ax+by$能够表示无限个以$1$为倍数的$c$。（甚至$x$或$y$可以取负数，来表示某些比较小的正整数$c$）。但当$x,y\geq0$时，有部分（有限个数的）$c$无法被$a,b$表示出来了。
+ 若$a,b$不互质，对于某个$c'$，不一定存在解$x,y$。因为$ax+by$只能表示**无限**个以$gcd(a,b)$倍数的$c$，但同时也有**无限**个不是以$gcd(a,b)$倍数的$c'$，无法被$a,b$表示出来！

由上面的推论，针对该题，要判断是否有有限个数字能够凑出来，关键看$A_1,A_2,...,A_n$的最大公因数是否不为$1$（即都互质）。



如何统计**不能够**表示出来的数字的**有限个数**呢？由于数据范围很小，我们假设总共有$100$种蒸笼，且每种蒸笼都恰好能装$100$个包子。要将每一种蒸笼只用一次的话，能够凑出$10000$个包子。换言之，我们只需要关注$[1,10000]$个包子能否被表示出来就可以了，如果超过$10000$个包子，同样能够分成两个子集，一个子集是若干组$10000$个包子，一个子集是不超过$10000$的包子。不再需要重复讨论。

因为每种蒸笼可以用无限个，要凑成特定数量的包子，我们能够联系到完全背包问题的解法。

```c++
#include <string>
#include <cstring>
#include <cstdio>
#include <iostream>
#include <stack>
#include <cmath>
#include <queue>
#include <map>
#include <vector>
#include <deque>
#include <algorithm>
#include <unordered_map>
using namespace std;
using ll = long long;
const int MAXN = 1e5 + 5;
int a[105], n, mygcd = 1;
bool dp[MAXN];
int gcd(int a, int b){
    if(b == 0) return a;
    else return gcd(b, a % b);
}
int main(){
    scanf("%d", &n);
    for(int i = 1; i <= n; i++){
        scanf("%d", &a[i]);
        mygcd = (i == 1) ? a[i] : gcd(mygcd, a[i]);
    }
    if(mygcd != 1) printf("INF\n");
    else {
        dp[0] = 1;
        int ans = 0;
        for (int i = 1; i <= n; i++) //种类
            for (int g = 0; g + a[i] <= 10000; g++) //背包重量
                if(dp[g]) dp[g + a[i]] = true;
        
        for(int i = 0; i <= 10000; i++)
            if(!dp[i]) ans++;
        printf("%d\n", ans);
    }
        
}
```

