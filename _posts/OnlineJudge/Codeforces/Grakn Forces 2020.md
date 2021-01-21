看到老外评论区中说，这场的难度估计是$div.1$和$div.1.5$的合并:pensive:

# A. Circle Coloring #构造

## [题目链接](https://codeforces.com/contest/1408/problem/A)

## 题意

给定三个长度为$n$数组$a,b,c$，要你从三个数组中选取元素构造出长度也为$n$的数组，内部相邻元素互不相等（包括下标$1$和$n$）

## 分析

注意是一个圈中相邻元素互不相等。

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
using namespace std;
typedef long long ll;
const int MAXN = 105;
const double EPS = 1e-7;
int q, n;
int a[MAXN], b[MAXN], c[MAXN], ans[MAXN];
int main(){
    scanf("%d", &q);
    while(q--){
        scanf("%d", &n);
        for (int i = 0; i < n; i++) scanf("%d", &a[i]);
        for (int i = 0; i < n; i++) scanf("%d", &b[i]);
        for (int i = 0; i < n; i++) scanf("%d", &c[i]);
        for (int i = 0; i < n; i++){
            ans[i] = a[i];
            if((ans[i] == ans[(i + 1) % n]) || (ans[i] == ans[(i - 1 + n) % n])){
                ans[i] = b[i];
                if((ans[i] == ans[(i + 1) % n]) || (ans[i] == ans[(i - 1 + n) % n])){
                    ans[i] = c[i];
                }
            } 
        }
        for (int i = 0; i < n; i++){
            printf("%d%c", ans[i], (i == n - 1) ? '\n' : ' ');
        }
    }
}
```

# B. Arrays Sum #贪心 #构造 #双指针

## [题目链接](https://codeforces.com/contest/1408/problem/B)

## 题意

给定一个非降数组$a$，需分解出若干个同$a$长度的子数组$b_i$（其中每个数组中的元素种类不超过给定的$k$），设分解出的子数组数量为$m$,使得对于$a_j(1\leq j\leq n)=b_{1,j} + b_{2,j} + ... + b_{m,j}$，现要你求出最小的$m$。（若找不出，则输出$m$）

## 分析

考虑到数据规模比较小，我们不妨每次先从当前$a$的每个元素中取出**最小元素**，直到不能再分解为止，由此得到的子数组最多含有两种元素（一种是当时的最小值，（存在前导零的情况）一种是$0$）。

接下来，$lo$指向含$0$少的子数组，$tot$指向当前含$0$多的子数组。依次将当前含$0$多的子数组（相应地$tot$指针往上移动）归入到含$0$少的子数组，直到$lo$指向数组的元素种数已到达要求，将$lo$指向下一个含$0$少的子数组。当两指针相遇时，算法即终止。这里之所以要先将含$0$较多的子数组归入含$0$少的子数组，这样是保证遍历时的顺序性的同时缩减规模。

我把我的思路做成下图：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20201001230446.png"/>

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20201001230513.png"/>

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
using namespace std;
const int MAXN = 105;
int q, n, k, a[MAXN];
bool check[MAXN]; //check[i]，表示分解的子数组bi是否存在前导零
int delByMin(){
    int diff = 0;
    bool f = false;
    for (int i = 1; i <= n; i++) {
        if(a[i] == 0)  
            f = true; //说明当前数组已经存在前导零
        else {
            diff = a[i]; //取得当前数组中第一个非零元素（即最小非零元素）
            break;
        }
    }
    if (diff == 0) return -1; //说明当前数组无非零元素
    for (int i = 1; i <= n; i++) {
        if (a[i] != 0)  a[i] -= diff; //用最小非零元素去减当前数组
    }
    return f; 
}
int main() {
    scanf("%d", &q);
    while (q--) {
        scanf("%d%d", &n, &k);
        int kin = 0; a[0] = -1; 
        for (int i = 1; i <= n; i++) {
            scanf("%d", &a[i]);
            if (a[i - 1] != a[i]) kin++; //统计原数组有多少种元素
        }
        if (k == 1 && kin > k) { printf("-1\n"); continue; }
        if (kin <= k)          { printf("1\n");  continue; }
        int tot = 0, lo = 0;
        while (1) {
            int f = delByMin(); if(f == -1) break;
            tot++; //分解数量
            check[tot] = f; //标记分解出的子数组有前导零
        }
        while (lo < tot) {
            lo++;
            int diff = min(k - 1 - check[lo], tot - lo);
            tot -= diff; //合并
        }
        printf("%d\n", lo);
    }
}
```

# C. Discrete Acceleration #二分答案 #模拟

## [题目链接](https://codeforces.com/contest/1408/problem/C)

## 题意

路长为$l$m，两车分别位于坐标$0$点，坐标$l$点，并以$1m/s$相向而行。路上有$n$个位于不同坐标的加速包，车一遇到即能速率$+1$，问两车何时相遇。允许误差不超过$1e-6$

## 分析

起初我尝试直接$O(n)$，但是发现有不少细节要考虑，尤其是当两车速率不再相等的同时，一边车能遇到密集的加速包，另一边车只遇到几个加速包，十分麻烦。

不妨二分答案，即二分两车相遇的时间$t$，然后分别计算两车在$t$s内的路程$x1、x2$，如果$x1+x2<l$，说明两车在$t$s内无法相遇，将$t$下界拔高；反之，将$t$上界降低。

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
using namespace std;
typedef long long ll;
const int MAXN = 1e5 + 5;
const double EPS = 1e-7;
int q, n, l, flag[MAXN];
bool Judge(double t) {
    int idx = 0;
    double x1 = 0, x2 = 0, curv = 1, tt = t;
    while (t > EPS && idx <= n) { //计算左车路程（从左到右）
        idx++;
        double diff = ((double)flag[idx] - x1); //求出下一加速包到当前坐标的距离
        x1 += min((diff * 1.0 / curv), t) * curv;
        t -= min((diff * 1.0 / curv), t); 
        curv++;  //加速
    } 
    idx = n + 1; curv = 1;
    while (tt > EPS && idx >= 1) {//计算右车路程（从右到左）
        idx--;
        double diff = (l - (double)flag[idx] - x2);
        x2 += min((diff * 1.0 / curv), tt) * curv;
        tt -= min((diff * 1.0 / curv), tt);
        curv++;
    }
    return (x1 + x2 >= l);
}
int main() {
    scanf("%d", &q);
    while (q--) {
        scanf("%d%d", &n, &l);
        for (int i = 1; i <= n; i++) scanf("%d", &flag[i]);
        flag[0] = 0; flag[n + 1] = l;
        double lo = 0, hi = l;
        while ((hi - lo) > EPS) {
            double mid = (lo + hi) * 1.0 / 2;
            if (Judge(mid)) hi = mid;
            else lo = mid;
        }
        printf("%lf\n", lo);
    }
}
```

# D. Searchlights #动态规划

## [题目链接](https://codeforces.com/contest/1408/problem/D)

## 题意

$n$个强盗分别位于坐标$(x_1,y_1),(x_2,y_2),...$，$m$个探照灯分别位于$(c_1,d_1),(c_2,d_2),...$。所有强盗的移动是**同步**的，每次移动可以向右一步或向上一步。若存在某个强盗$i$，他坐标中$x_i\leq c_j$**同时**$y_i\leq d_j$，则该强盗被探照灯$j$发现，说明不安全。你要求出最少的移动步数，使得所有强盗都安全。

## 分析

参考了官方题解，思考了好久qaq。

我们假设所有强盗都向右移动$x$，向上移动$y$后保证**安全**。显然可以推出，这样的$x,y$，对于任意强盗$(x_i,y_i)$、任意探照灯$(c_i,d_i)$，满足其中一个条件：要么$x+x_i>c_i$，要么$y+y_i>d_j$。如果前一条件不满足的话（即此时$x\leq c_i-x_i$），那么$y>d_j-y_i即y_i\geq d_j+b_i+1$。注意到$c_i-x_i\leq2\times 10^6$，我们不妨定义一个数组$diff$，当某个强盗$i$在**前一条件不满足**的情况下，专门记录他向右移动$c[j] - x[i]$（解决$i$的安全情况）之后，还需要向上移动（保证其他强盗也安全）的距离$d[j] - y[i] + 1$，为了应对多种探照灯的情况，必须找出强盗$i$向上移动的最大距离。

接下来我们就要找出“移动尽可能小的$x$步的同时移动尽可能小的$y$步”的情况，这样的情况即是题目要求。但注意代码中为何要从大到小枚举移动$x$距离呢？别忘了对于$x,y$的定义，这样是保证大部分强盗移动$y$步距离后能够安全的前提下，**逐步缩小**$x$步距离。

可能表达的思路还不够准确 ，未来还会过来更新下自己的理解。

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
const int MAXN = 2005;
int n, m, x[MAXN], y[MAXN], c[MAXN], d[MAXN];
int diff[2000005];
int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d%d", &x[i], &y[i]);
    for (int j = 1; j <= m; j++) scanf("%d%d", &c[j], &d[j]);
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (c[j] >= x[i]) //说明某一探照灯的x坐标比强盗i的x坐标大，不安全
                diff[c[j] - x[i]] = max(diff[c[j] - x[i]], d[j] - y[i] + 1);//更新y坐标增量
        }
    }
    int ans = 2000005, dy_max = 0;
    for (int dx = 1000005; dx >= 0; dx--) {  //向后枚举所有x增量
        dy_max = max(dy_max, diff[dx]);      //取得后缀中y增量的最大值（要保证y必须满足的前提）
        ans = min(ans, dx + dy_max);         //当前的x增量 与 后缀y增量最值 更新 移动次数
    }
    printf("%d\n", ans);
}
```

# E. Avoid Rainbow Cycles #并查集

## [题目链接](https://codeforces.com/contest/1408/problem/E)

## 题意

给定$m$长度的整数数组$a$、$n$长度的整数数组$b$、$m$个整数**集合**：$A_1,A_2,...,A_m$，每个集合元素在$1,2,...,n$范围内。每次操作中，如果你删除$A_i$中元素$j$（注意，不是第$j$个元素），要付出$a_i+b_j$的代价。删完后，对每一集合$A_i$内部所有元素两两建边$x\leftrightarrow y（其中x<y）$，且这些边颜色均为$i$。现要你用**最少的代价**建图，保证图中**不存在**一种含**不同颜色**边的环。

## 分析

我们可以发现，如果要保证图中不存在彩色环，就说明不可能有两个顶点，同时属于多个集合（说明两顶点间有多种颜色的边），最多只能属于一个集合。在建图的过程中，如果遇到这种情况，那就应该从代价小的集合中删去这两点，只在代价最大的集合保留这两点。那么问题就进一步转化为判断某**一**点是否属于多个颜色集合了，显然需要并查集。

由上面的分析，我们不用将每个集合中任意满足要求的两点进行建边，而是将颜色集合与普通整数点进行建边（边权取自删除该点所耗费的代价），数据规模减少了。先建边权大的边，一旦发现某个点已经加入某个颜色集合的同时又属于另一颜色集合，则删去这另一颜色集合，并计入耗费的代价。

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
typedef long long ll;
const int MAXN = 1e5 + 5;
int n, m;
int a[MAXN], b[MAXN], tot = 0, fa[MAXN << 1];
struct Edge{
    int u, v, w;
} E[MAXN << 1];
bool cmp(const struct Edge& a, const struct Edge& b){
    return a.w > b.w;
}
int findSet(int x){
    if(x != fa[x]) fa[x] = findSet(fa[x]);
    return fa[x];
}
int main(){
    scanf("%d%d", &m, &n);
    for (int i = 1; i <= m; i++) scanf("%d", &a[i]);
    for (int i = 1; i <= n; i++) scanf("%d", &b[i]);
    for (int i = 1, sz; i <= m; i++){ //第i个集合（即第i种颜色）
        scanf("%d", &sz);
        for(int j = 1, x; j <= sz; j++){ //A_i的第j个元素
            scanf("%d", &x);
            E[++tot] = {i, m + x, a[i] + b[x]}; //此处的x要加上m，是避免顶点编号与集合编号重合
        }
    }
    for (int i = 1; i <= n + m; i++) fa[i] = i;
    sort(E+1, E+1+tot, cmp); //按代价从大到小排序
    ll ans = 0;
    for (int i = 1; i <= n + m; i++) fa[i] = i;
    for (int i = 1; i <= tot; i++){
        int pu = findSet(E[i].u), pv = findSet(E[i].v);
        if(pu != pv)  //说明顶点v之前未加入任何一个颜色集合
            fa[pv] = pu;
        else  //说明v在两种颜色集合中，会出现彩虹环，要删
            ans += (ll)E[i].w;
    }
    printf("%lld\n", ans);
    return 0;
}
```



