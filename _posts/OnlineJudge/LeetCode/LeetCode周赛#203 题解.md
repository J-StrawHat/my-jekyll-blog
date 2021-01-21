# LeetCode周赛#203 题解

# 1561. 你可以获得的最大硬币数目 #贪心

## [题目链接](https://leetcode-cn.com/problems/maximum-number-of-coins-you-can-get/)

## 题意

有 3n 堆数目不一的硬币，你和你的朋友们打算按以下方式分硬币：

+ 每一轮中，你将会选出 任意 3 堆硬币（不一定连续）。
+ Alice 将会取走硬币数量最多的那一堆。
+ 你将会取走硬币数量第二多的那一堆。
+ Bob 将会取走最后一堆。

重复这个过程，直到没有更多硬币。
给你一个整数数组`piles` ，`piles[i]` 是第 `i` 堆中硬币的数目。现要你求出可获得的最大硬币数目。

## 分析

毫无疑问，需要先将硬币堆降序排序。既然每一轮我只能拿当前三堆中的第二大，为了让我的硬币数更多，应先让Bob从后面堆中取极小硬币堆，我与Alice在前面取极大硬币堆。

![](https://gitee.com/j__strawhat/MyImages/raw/master/20200825135643.png)

```c++
class Solution {
public:
    int maxCoins(vector<int>& piles) {
        sort(piles.begin(), piles.end());
        reverse(piles.begin(), piles.end());
        int cnt = 0, sum = 0;
        int L = 0, R = piles.size() - 1;
        for (int L = 1; L < piles.size() && L < R; L += 2, R--){
            sum += piles[L];
        }
        return sum;
    }
};
```

# 1562. 查找大小为M的最新分组 #二分思想 #STL #逆向思维

## [题目链接](https://leetcode-cn.com/problems/find-latest-group-of-size-m/)

## 题意

给定一数组 `arr` ，该数组表示一个从 1 到 n 的数字排列。有一个长度为 n 的二进制字符串，该字符串上的所有位最初都设置为 0 。在从 1 到 n 的每个步骤 `i `中（从 1 开始索引），二进制字符串上位于位置 `arr[i]` 的位将会设为 1 。

给定整数 m ，现要找出二进制字符串上存在**长度为 m**的一组 `"1"` 的**最后步骤**。一组 `"1" `是一个连续的、由 1 组成的子串，且左右两边不再有可以延伸的 1 。如果不存在这样的步骤，请返回 -1 。

## 样例

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20200825140057.png" >

## 分析

$O(n)$的解法可[戳此](https://leetcode-cn.com/problems/find-latest-group-of-size-m/solution/on-de-jie-jue-fang-fa-by-time-limit/)，此处提供易写的版本，同时学习`set`的 用法，不过复杂度约为$O(nlogn)$。

既然它要我们找出“最后”的步骤，可以尝试逆向思考，即从全为`"111…11"`串转化为`"110010..."`串，一旦串中出现长度恰为m的"1"子串即可返回。

如何在每一次操作中计算每个"1"串的长度？我们可以利用`set`的自动排序以及它自带的二分函数，将每一次**置0的位置**插入到`set`中，插入时需要通过二分（尽量使用`set`自带的二分函数，因为通用的二分函数不支持随机访问的容器，使得复杂度达到$O(n)$），主要是为了找到它的新插入位置附近的迭代器，通过对迭代器代表的位置做差即可得到特定"1"串的长度。

```c++
class Solution {
private:
    int sum[505] = {0};
    set<int> vis;
public:
    int findLatestStep(vector<int>& arr, int m) {
        int len = arr.size();
        if(len == m) return m;
        vis.emplace(0); //相比于insert(),emplace()直接构造对象，效率更高
        vis.emplace(len + 1);
        for (int i = len - 1; i >= 0; i--){
            auto it = vis.upper_bound(arr[i]); //返回第一个大于arr[i]的元素迭代器
            int L = *prev(it), R = *it; //prev()获取一个距离指定迭代器 n 个元素的迭代器，n取正数时，向左移动。
            if(arr[i] - L - 1 == m || R - arr[i] - 1 == m) //注意，要-1，因为是两个0位之差
                return i;
            vis.emplace(arr[i]);  //分裂成两堆
        }
        return -1;
    }
};
```

# 1563. 石子游戏 #记忆化搜索

## [题目链接](https://leetcode-cn.com/problems/stone-game-v/)

## 题意

几块石子排成一行 ，每块石子都有一个关联值，关联值为整数，由数组`stoneValue `给出。

游戏中的每一轮：Alice 会将这行石子分成两个 非空行（长度不一定相等）；Bob 负责计算每一行的值，即此行中所有石子的值的总和。Bob 会丢弃值最大的行，Alice 的得分为剩下那行的值（每轮累加）。如果两行的值相等，Bob 让 Alice 决定丢弃哪一行。下一轮从剩下的那一行开始。当只剩下一块石子时，游戏结束。Alice 的分数最初为 0 。

现要求 Alice 能够获得的最大分数 。

## 样例

![](https://gitee.com/j__strawhat/MyImages/raw/master/20200825142715.png)

## 分析

通过记忆化搜索来模拟即可，转移方程分三种情况，见下方代码。

```c++
class Solution {
private:
    int sum[505] = {0}, dp[505][505] = {0};
    int len;
public:
    int dfs(int lo, int hi){
        int mymax = 0;
        if(lo <= 0 || hi > len) return 0;
        if(hi - lo <= 0) return 0;
        if(dp[lo][hi] > 0) return dp[lo][hi];
        for (int i = lo; i <= hi; i++){
            int presum = sum[i] - sum[lo - 1], latsum = sum[hi] - sum[i];
            if(presum == latsum)
                mymax = max(mymax, max(dfs(lo, i), dfs(i + 1, hi)) + presum);
            else if(presum > latsum)
                mymax = max(mymax, dfs(i + 1, hi) + latsum);
            else
                mymax = max(mymax, dfs(lo, i) + presum);
        }
        return dp[lo][hi] = mymax;
    }
    int stoneGameV(vector<int>& stoneValue) {
        len = stoneValue.size();
        if(len == 1) return 0;
        if(len == 2) return min(stoneValue[0], stoneValue[1]);
        memset(dp, -1, sizeof(dp));
        for (int i = 0; i < len; i++) sum[i + 1] = sum[i - 1 + 1] + stoneValue[i];
        return dfs(1, len);
    }
};
```

