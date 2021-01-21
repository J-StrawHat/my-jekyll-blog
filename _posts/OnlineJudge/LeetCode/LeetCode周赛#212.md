# 1631. 最小体力消耗路径 #并查集 #最短路径

## [题目链接](https://leetcode-cn.com/problems/path-with-minimum-effort/)

## 题意

给定一二维 `rows x columns` 的地图 `heights` ，其中 `heights[row][col]` 表示格子 $(row, col)$ 的高度。一开始你在最左上角的格子 $(0, 0)$ ，且你希望去最右下角的格子 $(rows-1, columns-1)$ （下标从 0 开始）。每次可以往 上，下，左，右 四个方向之一移动，你想要找到耗费 体力 最小的一条路径。

一条路径耗费的 体力值 是路径上相邻格子之间 高度差绝对值 的 **最大值** 决定的。

## 分析

法一：Dijkstra求单源最短路径。从起点出发，四个方向，松弛其邻接的边，将能够松弛的顶点加入堆。其中顶点$(x,y)$松弛的条件，即为$dis[x][y] > max(dis[u.i][u.j], diff)$，其中$diff$为$(x,y)$到$(u.i,u.j)$的边权。

```c++
typedef struct Node{
    int i, j, dis;
    bool operator < (const struct Node& others) const{
        return dis > others.dis;
    }
} node;
class Solution {
private:
    int n, m;
    int di[5] = {0, 1, 0, -1, 0};
    int dj[5] = {0, 0, -1, 0, 1};
    int dis[105][105];
public:
    int minimumEffortPath(vector<vector<int>>& heights) {
        n = heights.size();
        m = heights[0].size();
        memset(dis, 0x3f, sizeof(dis));
        dis[0][0] = 0;
        priority_queue<node> myque;
        myque.push({0, 0, 0});
        while(!myque.empty()){
            node u = myque.top();
            myque.pop();
            if (u.dis != dis[u.i][u.j]) continue;
            for (int t = 1; t <= 4; t++){
                int x = u.i + di[t], y = u.j + dj[t];
                if(x < 0 || x >= n || y < 0 || y >= m) continue;
                int diff = abs(heights[u.i][u.j] - heights[x][y]);
                if(dis[x][y] > max(dis[u.i][u.j], diff)){
                    dis[x][y] = max(dis[u.i][u.j], diff);
                    myque.push({x, y, dis[x][y]});
                }
            }
        }
        return dis[n - 1][m - 1];
    }
};
```

法二：并查集。首先预处理，遍历矩阵中每个点，将每个点的四个方向的邻接点进行建边，边权为其两点间的差值。建立边集后，按边权从小到大排序边集，然后按此顺序依次将边中的点进行合并，直到起点与终点连通时，所对应的边的边权即为题目所求。

```c++
typedef struct Edge{
    int u, v, w;
    bool operator < (const struct Edge& others) const {
        return w < others.w;
    }
} edge;
class Solution {
private:
    int fa[10005];
    int n, m;
public:
    int findSet(int x){
        if(x != fa[x]) fa[x] = findSet(fa[x]);
        return fa[x];
    }
    int minimumEffortPath(vector<vector<int>>& heights) {
        n = heights.size(), m = heights[0].size();
        vector<edge> E;
        for (int i = 0; i < (n * m); i++) fa[i] = i;
        for (int i = 0; i < n; i++){
            for (int j = 0; j < m; j++){
                int idx = i * m + j;
                if(i - 1 >= 0) E.push_back({idx, idx - m, abs(heights[i][j] - heights[i - 1][j])});
                if(j - 1 >= 0) E.push_back({idx, idx - 1, abs(heights[i][j] - heights[i][j - 1])});
            }
        }
        sort(E.begin(), E.end());
        for(int i = 0; i < E.size(); i++){
            int pu = findSet(E[i].u), pv = findSet(E[i].v);
            if(pu != pv) fa[pu] = pv;
            int st = findSet(0), ed = findSet(n * m - 1);
            if(st == ed) return E[i].w;
        }
        return 0; //避免图中只有一个顶点的情况
    }
};
```

# 1632. 矩阵转换后的秩 #并查集

## [题目链接](https://leetcode-cn.com/problems/rank-transform-of-a-matrix/)

## 题意

给你一个 `m x n` 的矩阵 `matrix` ，请你返回一个新的矩阵 `answer` ，其中 `answer[row][col]` 是 `matrix[row][col]` 的秩。

每个元素的 **秩** 是一个整数，表示这个元素相对于其他元素的大小关系，它按照如下规则计算：

+ 如果一个元素是它所在行和列的最小值，那么它的 **秩** 为 $1$。

+ 如果两个元素 $p$ 和 $q$ 在 **同一行** 或者 **同一列** ，那么：

  + 如果 $p < q$ ，那么 $rank(p) < rank(q)$

  + 如果 $p = q$ ，那么 $rank(p) == rank(q)$

    如果 $p > q$ ，那么 $rank(p) > rank(q)$ 秩 需要越 小 越好。

## 样例

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20201104092348.png"/>

## 分析

好多题解我都没看懂$QAQ$，感谢[@huanglin的代码](https://leetcode-cn.com/problems/rank-transform-of-a-matrix/solution/javadai-ma-de-pai-xu-bing-cha-ji-tong-su-yi-dong-b/)帮我弄清这题的思路。

如果给定的矩阵并不存在一个相同的元素的话，那么只需要从小到大排序就能排出秩。但是题目中最主要的问题，在于**同行、同列**的**相同**元素，会共享同一个秩。

如何解决？首先应将矩阵中的所有元素进行排序，然后按该顺序遍历每个顶点。将其对应的行、列中相同的元素合并在一个并查集中，然后再用当前的最大的秩号去更新这个并查集的代表元素（满足同行、同列的相同元素共享同一个秩）。

如果该顶点不存在行列相同的元素，那么就从当前行列的元素最大的秩号$+1$（满足 $p < q$ ，那么 $rank(p) < rank(q)$）。

```c++
class Solution {
private:
    int n, m;
    int fatherIdx[500 * 500 + 5], fatherVal[500 * 500 + 5] = { 0 };
public:
    int findSet(int x) {
        if (x != fatherIdx[x]) fatherIdx[x] = findSet(fatherIdx[x]);
        return fatherIdx[x];
    }
    void Union(int x, int y) {
        int px = findSet(x), py = findSet(y);
        if (px != py) fatherIdx[px] = py;
    }
    vector<vector<int>> matrixRankTransform(vector<vector<int>>& matrix) {
        n = matrix.size(); m = matrix[0].size();
        vector< pair<int, int> > matrixVal; //将二维矩阵转化为一维，first值存原矩阵值，second值存其i*m+j编号
        for (int i = 0; i < (n * m); i++)
            matrixVal.push_back({ matrix[i / m][i % m], i });
        sort(matrixVal.begin(), matrixVal.end()); //对一维压缩矩阵排序
        vector<int> toRow(n, -1), toCol(m, -1); //toRow[i]，代表第i行下【已知的】最大元素的列号；toCol反之
        for (int i = 0; i < (n * m); i++) fatherIdx[i] = i; //初始化【行列并查集】（也就说，行列相同的元素连在一起）

        for (int pos = 0; pos < (n * m); pos++) { //从小到大，遍历一维压缩矩阵
            int idx = matrixVal[pos].second; //获得第pos小的矩阵元素所属的原压缩位置
            int i = idx / m, j = idx % m;
            int val = 1;
            if (toRow[i] != -1) { //第i行中最大元素的列号
                int toIdx = i * m + toRow[i]; //压缩这个最大元素的状态
                int toFatherIdx = findSet(toIdx);
                int toFatherVal = fatherVal[toFatherIdx];
                if (matrix[i][j] == matrix[i][toRow[i]]) { //如果第i行有两个相同的值，
                    Union(idx, toIdx);//合并集合扩大规模
                    val = max(val, toFatherVal);
                }
                else
                    val = max(val, toFatherVal + 1);
            }
            if (toCol[j] != -1) { //第j行中最大元素的行号
                int toIdx = toCol[j] * m + j;
                int toFatherIdx = findSet(toIdx);
                int toFatherVal = fatherVal[toFatherIdx];
                if (matrix[i][j] == matrix[toCol[j]][j]) {
                    Union(idx, toIdx);
                    val = max(val, toFatherVal);
                }
                else
                    val = max(val, toFatherVal + 1);
            }
            toRow[i] = j; toCol[j] = i;
            int curFatherIdx = findSet(idx);
            fatherVal[curFatherIdx] = val; //将这个最大的val赋给并查集的代表元素
        }
        vector<vector<int>> res(n, vector<int>(m));
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                res[i][j] = fatherVal[findSet(i * m + j)]; //注意，一定要先找到并查集的代表元素
            }
        }
        return res;
    }
};
```

