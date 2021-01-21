这场有几道题目思路，在之前比赛中遇到过



# D. 数的分解 #枚举

## 题意

将$2019$分解成$3$个**各不相同**的正整数之和，并且每个正整数都不包含数字$2$和$4$，一共有多少种分解方法？注意，$1000+1001+18$和$1001+1000+18$被视为同一种

## 分析

由于三个正整数均不相同，也就说这三个正整数存在偏序关系，不妨设`i<j<k`，枚举`i`,`j`两个变量，推出`k`，判断`i`,`j`,`k`是否满足题目要求。

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
bool Judge(int x) {
    while (x) {
        int tmp = x % 10;
        if (tmp == 4 || tmp == 2) return false;
        x /= 10;
    }
    return true;
}
int main() {
    int ans = 0;
    for (int i = 1; i < 2019; i++) {
        if (!Judge(i)) continue;
        for (int j = i + 1; j < 2019; j++) {
            if (!Judge(j)) continue;
            int k = 2019 - i - j;
            if (j >= k || !Judge(k)) continue;
            ans++;
        }
    }
    printf("%d", ans);
    return 0;
}
```

# E. 迷宫 #广搜

## 题意

对于迷宫，从入口开始，可以按DRRURRDDDR 的顺序通过迷宫，
一共10 步。其中D、U、L、R 分别表示向下、向上、向左、向右走。请找出一种通过迷宫的方式，其使用的步数最少，在步数最少的前提下，请找出**字典序最小**的一个作为答案。

## 分析

刚好就是[牛客小白月赛#26的E题](https://www.cnblogs.com/J-StrawHat/p/13611023.html)

# G. 完全二叉树的权值 #树的层次遍历

## 题意

给定一棵包含N 个节点的完全二叉树，树上每个节点都有一个权值，按从上到下、从左到右的顺序依次是$A_1, A_2, …A_N$，如下图所示：

![image-20201014003915894](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201014003915894.png)

现在要把相同深度的节点的权值加在一起，想知道哪个深度的节点权值之和最大？如果有多个深度的权值和同为最大，请你输出其中最小的深度。注：根的深度是1。

## 分析

树的遍历方式其实与[LeetCode周赛#209的题1609](https://www.cnblogs.com/J-StrawHat/p/13780573.html)相同，只不过该题不需要指针，直接通过数组下标即可得到左右孩子的数据了。外层循环迭代树的深度，内层循环遍历树同一深度下的所有节点（从左到右），计算当前深度下所有节点的深度。

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
using namespace std;
using ll = long long;
const int MAXN = 1e5 + 5;
const int INF = 0x3f3f3f3f;
int tree[MAXN*4], n;
int ans_d; ll ans = -1;
void BFS(){
    queue<int> myque;
    myque.push(1);
    int depth= 1;
    while(!myque.empty()){
        int m = myque.size();
        int cur;
        ll sum = 0;
        while(m--){
            cur = myque.front(); myque.pop();
            if(tree[cur<<1]<INF) myque.push(cur<<1);
            if(tree[cur<<1|1]<INF) myque.push(cur<<1|1);
            sum += (ll)tree[cur];
        }
        if(sum > ans){
            ans = sum;
            ans_d = depth;
        }
        depth++;
    }
}
int main(){
    scanf("%d", &n);
    memset(tree, 0x3f, sizeof(tree));
    for(int i = 1; i <= n; i++) scanf("%d", &tree[i]);
    BFS();
    printf("%d\n", ans_d);
}
```

# H. 等差数列 #数学

## 题目

数学老师给小明出了一道等差数列求和的题目。但是粗心的小明忘记了一部分的数列，只记得其中$N$个整数。现在给出这$N $个整数，小明想知道**包含这$N $个整数**的最短的等差数列有几项？

## 分析

顺便复习一下[CodeForces#667的C题](https://www.cnblogs.com/J-StrawHat/p/13618427.html)

这题先将数组中所有数升序排序，然后相邻两元素作差存入新数组中，找到新数组所有元素的最大公约数即可。但注意，有可能公差为0，即原数组所有数均相等。

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
using namespace std;
using ll = long long;
const int MAXN = 1e5 + 5;
const int INF = 0x3f3f3f3f;
int gcd(int a, int b){
    if(b == 0) return a;
    else return gcd(b, a % b);
}
int a[MAXN], n, d = 0;
int main(){
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) scanf("%d", &a[i]);
    sort(a+1, a+1+n);
    for(int i = 2; i <= n; i++){
        int diff = a[i] - a[i - 1];
        d = gcd(d, diff);
    }
	int ans = (d == 0) ? n : ((a[n] - a[1]) / d + 1);
    printf("%d\n", ans);
}
```

# I. 后缀表达式 #性质观察

## 题意

给定$N$个加号、$M$个减号以及$N+M+1$个整数$A_1,A_2,…, A_{N+M+1}$，小明想知道在所有由这$N$个加号、$M$个减号以及$N+M+1$个整数凑出的合法的后缀表达式中，结果最大的是哪一个？

## 分析

不要简单地认为，直接排序再贪心就可以了。

注意，后缀表达式（逆波兰表达式）的用途实际上就是不需要使用括号，就能表示带优先级的运算关系。例如

```c++
0! + 123 + 4 * (5 * 6! + 7!/8) / 9
其后缀表达式为：0! + 123 + 456！* 7 ！ 8/ + * 9 / +
```

因而，该题目实际上隐藏了额外条件，你可以对表达式任意加上括号，任意调整正负运算符（但负号不能作为一元运算符）。由此，我们就可以充分利用括号与负号运算符，将原整数中的负数取为正数，得到比直接排序贪心更大的结果！接下来，我们就对原整数中的正负情况进行讨论（在$m\not=0$的情况）。

如果$A_1,A_2,…, A_{N+M+1}$既含正也含负，我们**总可以**将所有负数置为正数，也就说我们直接将所有数累加即是答案。比如：

```c++
n=3, m=3
A = {1 -2 -2 -3 -4 -5 -6}
1 -((-2)+(-2)+(-3)+(-4)) -(-5) -(-6) //最终结果即为所有数绝对值之和
    

n=3, m=4
A = {1 3 -2 -2 -3 -4 -5 -6}
1 + 3 - ((-2) + (-2) + (-3)) -(-4) -(-5) -(-6) //最终结果即为所有数绝对值之和
```

如果$A_1,A_2,…, A_{N+M+1}$全为正数，我们总可以将大部分负号给消掉，只剩下一个负号，留给**最小正整数**。也就说，我们将所有数（除了最小值）的之和，再加上最小值的相反数，即为答案。比如：

```c++
n=3, m=3
A = {1, 3, 3, 3, 4, 4, 5}
5 + 3 + 4 + 4 -(1 -(3) -(3)) //最终结果即为所有数绝对值之和-最小值*2（其中最小值为1）
    
n=3, m=4
A = {1, 3, 3, 4, 4, 5, 5, 5}    
5 + 3 + 4 + 5 -(1 -(3) -(4) -(5)) //最终结果即为所有数绝对值之和-最小值*2（其中最小值为1）
```

如果$A_1,A_2,…, A_{N+M+1}$全为负数，我们总可以将大部分负号给消掉，只剩下一个负号，留给**最大负整数**。也就说，我们将所有数的**绝对值**（除了最大的负数）之和，再加上这个最大的负数（尽可能减少总和的影响），即为答案。比如：

```c++
n=3, m=3
A = {-1, -3, -3, -3, -3, -3, -3} //最终结果即为所有数绝对值之和+最大值*2（其中最大值为-1）
(-1) - ( (-3) + (-3) + (-3) + (-3) ) - (-3) - (-3)
```



于是最终的代码十分简洁：

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
using namespace std;
const int MAXN = 1e5 + 5;
const int INF = 0x3f3f3f3f;
using ll = long long;
ll a[MAXN << 1], mymin = INF, mymax = -INF, sum = 0;
int main() {
    int n, m; scanf("%d%d", &n, &m);
    for (int i = 1; i <= n + m + 1; i++) {
        scanf("%lld", &a[i]);
        mymin = min(mymin, a[i]);
        mymax = max(mymax, a[i]);
    }
    if (m == 0) {
        for (int i = 1; i <= n + m + 1; i++) sum += a[i];
    }
    else {
        for (int i = 1; i <= n + m + 1; i++) sum += abs(a[i]);
        if (mymin >= 0) sum -= mymin * 2;
        if (mymax <= 0) sum += mymax * 2;
    }
    printf("%lld\n", sum);
    return 0;
}
```