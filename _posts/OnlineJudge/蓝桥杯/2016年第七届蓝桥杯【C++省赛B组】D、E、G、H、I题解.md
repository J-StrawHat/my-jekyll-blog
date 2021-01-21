# F. 方格填数 #深搜

## 题意

有$10$个格子，填入0~9的数字。要求：**连续的两个数字**不能相邻。（左右、上下、对角都算相邻），求可能的填数方案数。

```c++
   +--+--+--+
   |  |  |  |
+--+--+--+--+
|  |  |  |  |
+--+--+--+--+
|  |  |  |  
+--+--+--+
```

## 分析

题目有点表述不清，实际上要我们在所有格子中只填一种数字。题目中的“相邻”，是指元素在位置上的相邻。而“连续”，是指数字意义上的连续，比如`4 5 7`，中间的`5`，它与右边的`7`不是连续的，但它与左边的`4`连续，即$5 - 4 = 1$。因此，在判断是否连续的时候，需要判断特定点的左、左上、上、右上的数字，与特定点的绝对值之差是否为$1$，若为$1$则说明相邻数字是连续的，不合题意。

由于数据规模不大，我们从第一行第二列开始搜，第三行第三列作为终点。

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
const int MAXN = 105;
int Ma[5][5], ans = 0, used[11];
int dx[] = {0, -1, -1, -1};
int dy[] = {-1, -1, 0, 1}; //左、左上、上、右上
bool Judge(int x, int y, int num){ //四个方向判断该点是否满足题意要求
    for(int t = 0; t < 4; t++){
        int nx = x + dx[t], ny = y + dy[t];
        if(1 <= nx && nx <= 3 && 1 <= ny && ny <= 4 && abs(Ma[nx][ny] - num) == 1)
            return false;
    }
    return true;
}
void DFS(int x, int y){
    if(x >= 3 && y >= 4){
        ans++;
        return;
    }
    for(int i = 0; i < 10; i++){ //枚举数字
        if(used[i]) continue; //之前路径已经使用过该数字
        if(Judge(x, y, i)){
            Ma[x][y] = i;
            used[i] = true; 
            if(y + 1 <= 4) DFS(x, y + 1);
            else DFS(x+1, 1); //列数已达到边界，到下一行的第一列
            used[i] = false;
        }
    }
}
int main(){
	for(int i = 0; i <= 4; i++)
        for(int j = 0; j <= 4; j++) Ma[i][j] = -2; //填个不会与0-9相差一的数字。
	DFS(1, 2); //从第一行第二列开始搜
	cout << ans << endl;
	//cout << 1580 << endl;
    return 0;
}
```

# G. 剪邮票 #枚举 #连通性 

## 题意

有$12$张连在一起的$12$生肖的邮票。现在你要从中剪下$5$张来，要求必须是连着的。（仅仅连接一个角不算相连）现要你计算共有多少种不同的剪取方法。

![image-20201016104222399](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201016104222399.png)

## 分析

要注意到邮票当中每个格子的数字是不相同的！只要选定的路径不同，就代表了不同的剪取方法了。

不妨定义个状态表，如`a[12] = {0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1};`，代表一开始的时候，第$0$到$6$个格子"不使用"，第$7$到$11$个格子会"使用"。接下来就用全排列函数去枚举这个状态表。

在当前的状态表下，我们就要判断在实际位置中"使用"的格子，是否是连通的！如何判断连通？从某一个“使用”的格子出发，深搜去寻找每一个有标记“使用”的格子。如果“使用”的格子是相互连通的，按理说，只需要深搜一次，否则，就是不连通的。

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
int ans = 0, used[5][5];
int a[12] = {0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1};
int dx[] = {1, 0, -1, 0};
int dy[] = {0, -1, 0, 1};
void DFS(int x, int y){
    for(int t = 0; t < 4; t++){
        int nx = x + dx[t], ny = y + dy[t];
        if(1 <= nx && nx <= 3 && 1 <= ny && ny <= 4 && used[nx][ny]){ 
            used[nx][ny] = 0; //消除标记
            DFS(nx, ny);
        }
    }
}
int main(){
    do{
        memset(used, 0, sizeof(used));
        int k = 0, cnt = 0;
        for(int i = 1; i <= 3; i++)
            for(int j = 1; j <= 4; j++)
                used[i][j] = a[k++];
        for(int i = 1; i <= 3; i++){
            for(int j = 1; j <= 4; j++){
                if(used[i][j]){ //找到特定点
                    cnt++;
                    DFS(i, j); //从该特定点出发，去将所有与之连通的点进行访问，并消除它们的标记
                }
            }
        }
        if(cnt == 1) ans++; //说明只有一个连通块
    }while(next_permutation(a, a + 12)); //枚举这12个数的全排列
    printf("%d\n", ans);
}
```

# H. 四平方和 #暴力 #哈希表

## 题意

每个正整数都可以表示为至多$4$个**正整数**的平方和。如果把0包括进去，就正好可以表示为$4$个数的平方和。如：

$ 5 = 0^2 + 0^2 + 1^2 + 2^2 $
		$7 = 1^2 + 1^2 + 1^2 + 2^2$

对于一个给定的正整数$N$($\leq 5000000$)，可能存在多种平方和的表示法。要求你对4个数**排序**：$0 \leq a \leq b \leq c \leq d$
并对所有的可能表示法按 $a,b,c,d$ 为联合主键升序排列，最后输出**第一个表示**法。

## 分析

该题在NOJ中三重循环可以过，这里给一下$O(\sqrt{N}\times\sqrt{N})$的代码。（感谢为神:smile:）

我们一开始容易想到枚举$a,b,c$，然后通过$\sqrt{N-a^2-b^2-c^2}$确定$d$。我们能不能将枚举$c$的那层循环优化掉？我们可以个哈希表去存下每个$c^2+d^2$所对应的$c$，也就说，定义哈希表`mymap[]`（普通数组模拟下即可），则`mymap[c*c+d*d]=c`，首先要预处理，为所有$c^2+d^2$结果记录其所对应的$c$，接下来我们只需要枚举$a,b$，计算$cur=\sqrt{N-a^2-b^2}$，然后用哈希表读出$cur$所对应的$c$，再用$\sqrt{N-a^2-b^2-c^2}$确定$d$即可。注意，枚举过程中要保证$0 \leq a \leq b \leq c \leq d$

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
const int MAXN = 1e7 + 5;
const int INF = 0x3f3f3f3f;
int mymap[MAXN];
int main() {
    int mymax=(int)sqrt(5000000)+1, n;
    memset(mymap, 0x3f, sizeof(mymap));
    for (int i = 0; i <= mymax; i++) {
        for (int j = i; j <= mymax; j++) {
            int cur = i * i + j * j;
            if (cur >= 1e7) continue;
            if (mymap[cur] == INF) mymap[cur] = i;
        }
    }
    while (~(scanf("%d", &n))) {
        mymax = (int)sqrt(n) + 1;
        for (int a = 0; a <= mymax; a++) {
            for (int b = a; b <= mymax; b++) {
                int last = n - a * a - b * b; if (last < 0) continue;
                if (mymap[last] < INF) { 
                    int c = mymap[last]; //哈希表查询c
                    int d = (int)(sqrt(last - c * c)); //计算得到d
                    if(b <= c && c <= d){ //保证顺序
                        printf("%d %d %d %d\n", a, b, c, d);
                        a = b = n + 1; //终止循环
                    }
                }
            }
        }
    }
    return 0;
}

```



# J. 交换瓶子 #贪心

## 题意

有$N$个瓶子，编号$ 1$ ~ $N$，放在架子上。要求每次拿起2个瓶子，交换它们的位置。经过若干次后，使得瓶子的序号为：$1\ 2\ 3\ 4\ 5$。现要你求出最少的交换次数。

## 分析

注意，该题是求最少交换次数，并不是求逆序对。题目思路与[LeetCode 周赛 1536 排布二进制网格的最少交换次数](https://www.cnblogs.com/J-StrawHat/p/13430812.html)基本一致。不过周赛那题，要求的是降序（本题要求的是**升序**），要求也更高一些，会出现相同的元素，交换操作只允许相邻行元素交换，在本题中是严格递增且可以任意交换。

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
int a[MAXN], n;
int main(){
    while(~(scanf("%d", &n))){
        int ans = 0;
        for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
        for (int i = 1; i <= n; i++){
            if(a[i] == i) continue;
            int pos = i;
            for (int j = i + 1; j <= n; j++){
                if(a[j] == i){
                    pos = j;
                    break;
                }
            }
            swap(a[i], a[pos]);
            ans++;
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

