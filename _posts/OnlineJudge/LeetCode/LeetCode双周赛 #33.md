#  LeetCode双周赛 #33

# 5480. 可以到达所有点的最少点数目

## [题目链接](https://leetcode-cn.com/problems/minimum-number-of-vertices-to-reach-all-nodes/)

## 题意

给定**有向无环**图，编号从`0`到`n-1`，一个边集数组`edges`（表示从某个顶点到另一顶点的有向边），现要找到**最小**的顶点集合，使得从这些点出发，能够到达图中所有顶点。

## 样例

![](https://gitee.com/j__strawhat/MyImages/raw/master/5480e2.png)

输出为`[0, 2, 3]`。从这三个顶点出发即能访问所有顶点。

## 分析

实际上，只需要将所有入度为0的顶点加入解集即可。因为：1.入度为0的顶点若不加入解集，则除了它以外，没有其他顶点能够沿途访问到它。2.入度不为0的顶点一定能被某个顶点沿途访问到，为了保证解集尽可能小，那应尽可能从这些入度非0的顶点溯源。

```c++
class Solution {
private:
    int InD[100005] = { 0 };
public:
    vector<int> findSmallestSetOfVertices(int n, vector<vector<int>>& edges) {
        for (int i = 0; i < edges.size(); i++) {
            int cur = edges[i][1];
            InD[cur]++;
        }
        vector<int> ans;
        for (int i = 0; i < n; i++) {
            if (InD[i] == 0) {
                ans.push_back(i);
            }
        }
        return ans;
    }
};
```



# 5481. 得到目标数组的最少函数调用次数 #逆向思维 #贪心

## [题目链接](https://leetcode-cn.com/problems/minimum-numbers-of-function-calls-to-make-target-array/)

## 题意

你可以对数组`arr`进行两类操作：

1. 对数组某个元素进行自增
2. 对数组**每个**元素的值`*2`

先给定一个`nums`大小相同且初始值全为0的数组`arr`，现要求需要多少次操作能将`arr`转变为`nums`。

## 分析

本题看似较为麻烦，实际上逆向去思考，如果当前数组存在奇数，上一次操作必然不是`*2`而只能是对某个数进行自增；如果当前数组全为偶数，由贪心思维，若上一次操作是对数组所有元素都进行`*2`，能够保证操作次数不会太多。

```c++
class Solution {
public:
    int minOperations(vector<int>& nums) {
        int ans = 0;
        while(1){
            bool f = false;
            int cnt = 0;
            for (int i = 0; i < nums.size(); i++){
                if(nums[i] % 2 == 1){
                    ans++;
                    nums[i]--;
                    f = true; /*说明当前数组存在奇数*/
                }
                if(nums[i] == 0) cnt++;
            }
            if(cnt == nums.size()) break;
            if(f == false){ /*说明当前数组全为偶数*/
                for(int i = 0; i < nums.size(); i++){
                    nums[i] >>= 1;
                }
                ans++;
            }
        }
        return ans;
    }
};
```

# 5482. 二维网格图中探测环 #暴力搜索

## [题目链接](https://leetcode-cn.com/problems/detect-cycles-in-2d-grid/)

## 题意

给定二维字符网格数组`grid`，大小$n\times m(n,m\leq500)$，需检查`grid`是否存在**相同值**形成的环。

其中环的定义是，一条开始和结束于同一个格子的长度 **大于等于 4** 的路径。而这条路径只能上、下、左、右四个方向移动，不能斜向移动。

## 样例

输入：`grid = [["c","c","c","a"],["c","d","c","c"],["c","c","e","c"],["f","c","c","c"]]`

![](https://gitee.com/j__strawhat/MyImages/raw/master/5482e22.png)

输出：`true`

## 分析

考虑到数据范围不是太大，可以枚举二维矩阵每个元素作为起点。然后从该起点出发，搜索可以延伸的路径，直到碰到原来起点或者原来访问过的节点。

```c++
class Solution {
private:
    int di[5] = { 0, 0, 1, 0, -1 };
    int dj[5] = { 0, 1, 0, -1, 0 };
    int vis[505][505] = { 0 };
public:
    bool dfs(vector<vector<char>>& grid, int x, int y, int prex, int prey, int begx, int begy) { //(x,y)表示当前点，(prex,prey)表示上一转移的点，(begx,begy)表示原来起点
        vis[x][y] = 1;
        int n = grid.size(), m = grid[x].size();
        for (int t = 1; t <= 4; t++) {
            int curx = x + di[t], cury = y + dj[t];
            if (prex == curx && prey == cury) continue; //防止回退
            else if (0 > curx || curx >= n || 0 > cury || cury >= m) continue; //防止越界
            else if (curx == begx && cury == begy)  return true; //抵达起点
            else if (grid[curx][cury] == grid[x][y]) { //相同字符的条件下
                if (vis[curx][cury]) return true; //别忘了该条语句。说明点(x,y)周围遇到相同字符的元素,可以形成环。减少时间复杂度
                if (dfs(grid, curx, cury, x, y, begx, begy)) return true;//再搜索
            } //不需要回溯，将原来访问过的节点进行状态重置，否则会超时
        }
        return false;
    }
    bool containsCycle(vector<vector<char>>& grid) {
        for (int i = 0; i < grid.size(); i++) {
            for (int j = 0; j < grid[i].size(); j++) {
                if (!vis[i][j] && dfs(grid, i, j, -1, -1, i, j))
                    return true;
        return false;
    }
};
```



