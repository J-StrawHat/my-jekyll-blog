# 1589. 所有排列中的最大和 #差分 #贪心

## [题目链接](https://leetcode-cn.com/problems/maximum-sum-obtained-of-any-permutation/)

## 题意

给定整数数组`nums`，以及查询数组`requests`，其中`requests[i] = [starti, endi] `。第` i `个查询求 `nums[starti] + nums[starti + 1] + ... + nums[endi - 1] + nums[endi] `的结果 。

你可以**任意排列** `nums` 中的数字，请你返回所有查询结果之和的**最大**值,请将答案对 109 + 7 取余 后返回。

## 分析

我们先离线获得需要查询的区间，并统计该查询涉及端点的出现次数（也就是频数）。显然由贪心思想，我们要想答案尽可能地大，就应该将值更大的元素安排到出现次数更多的端点上，即高频数$\times$大元素。

```c++
typedef long long ll;
const int MAXN = 1e5 + 5;
const int MOD = 1e9 + 7;
class Solution {
private:
    int cf[MAXN];
    vector<int> used;
public:
    int maxSumRangeQuery(vector<int>& nums, vector<vector<int>>& requests) {
        for (int i = 0; i < requests.size(); i++){
            int a = requests[i][0], b = requests[i][1];
            cf[a]++;
            cf[b + 1]--;
        }
        int sum = 0;
        for (int i = 0; i < nums.size(); i++){
            sum += cf[i];
            used.push_back(sum);
        }
        sort(used.begin(), used.end(), greater<int>());
        sort(nums.begin(), nums.end(), greater<int>());
        ll ans = 0;
        for (int i = 0; i < used.size(); i++){
            ans += ((ll)used[i] * (ll)nums[i]);
        }
        return ans % MOD;
    }
};
```



# 1590. 使数组和能被 P 整除 #前缀和 #哈希表

## [题目链接](https://leetcode-cn.com/problems/make-sum-divisible-by-p/)

## 题意

给定正整数数组 `nums`，请你移除 **最短** 子数组（即原数组中连续的一组元素，可以为 空），使得剩余元素的 **和** 能**被 p 整除**。 不允许 将整个数组都移除。要你返回需要移除的最短子数组的长度，如果无法满足题目要求，返回 -1 。

## 分析

令$lastmod = \sum{nums}\ \%\ p$，当$lastmod==0$显然无需操作，答案为0；当$lastmod\not= 0$，我们的问题接下来就变为从原数组中找到一个最短子数组，使得$\sum{(该子数组)}\% p = lastmod$，此时原数组剩余部分都能被$p$整除！

要求一段连续子数组之和，需要前缀和维护。不过注意这个和是经过模$p$运算处理的，这样是为了找到一段子数组**模$p$加法**运算得到上一段所说的$lastmod$。我们每遍历一个元素，就将其对应的模$p$前缀和$curmod$在哈希表中映射其对应位置，用于未来查询相应的$targetMod$的**位置**。如下图所示。

如何对当前$i$下已知的$curmod$求出对应的$targetMod$呢？当$curmod \geq lastmod$时，之前按下图所示，$targetMod = curmod - lastmod$；当$curmod\leq lastmod$时，就需要加上一个$p$，即$targetMod = curmod - lastmod + p$。上面两种情况合起来就是，$targetMod = curmod - lastmod + p$。我们求出$targetMod$的值，再通过哈希表查询$targetMod$的位置$pos$，此时长度为$pos-i$、区间$[pos, i]$的子数组，满足其和模$p$是等于$lastmod$的，更新长度，直到所有元素遍历完，便得到最短长度了。

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20200922132959.png"/>

此处初始化有个重要的小细节，之所以要`pos[0] = -1;`，是因为在**第一次**的`targetMod==0`即`curmod==lastmod`时，你从哈希表查询到的位置刚好也是`lastmod`（刚刚更新的位置），更新的长度就变成`0`了！！显然它应该更新长度是从`[0, lastmod位置]`

```c++
typedef long long ll;
class Solution {
private:
    unordered_map<ll, int> pos; //用于记录总和取了模之后的最新位置，即离当前元素最近的位置，保证题目的“最短”
public:
    int minSubarray(vector<int>& nums, int p) {
        ll sum = 0;
        for(int i = 0; i < nums.size(); i++) sum += (ll)nums[i];
        ll lastmod = sum % (ll)p, curmod = 0;
        if(lastmod == 0) return 0; //直接返回
        if(sum < p) return -1; //和 比 模数更小，显然无法满足题目要求
        pos[0] = -1;//一定要先初始化-1！！！
        int minlen = 0x3f3f3f3f;
        for(int i = 0; i < nums.size(); i++){
            curmod = (curmod + nums[i]) % p;
            pos[curmod] = i;
            ll targetMod = (curmod - lastmod + p) % p;
            if(pos.count(targetMod))
                minlen = min(minlen, i - pos[targetMod]);
        }
        return (minlen < nums.size()) ? minlen : -1;
    }
};
```



# 1591. 奇怪的打印机 II #拓扑排序

## [题目链接](https://leetcode-cn.com/problems/strange-printer-ii/)

## 题意

一个打印机有如下两个特殊的打印规则：

+ 每一次操作时，打印机会用同**一种颜色**打印**一个矩形**的形状，每次打印会**覆盖**矩形对应格子里原本的颜色。
+ 一旦矩形根据上面的规则使用了一种颜色，那么 相同的颜色不能再被使用 。

给定 `m x n` 的矩形 `targetGrid` ，其中 `targetGrid[row][col] `是位置 `(row, col)` 的颜色。如果能按上述规则打印出矩形` targetGrid `，返回 `true `，否则返回` false` 。

## 题目分析

注意到数据范围均在$60$以内，意味着我们可以枚举所有的颜色。

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20200922133035.png"/>

```c++
class Solution {
private:
    int bottom[65], top[65], left[65], right[65];
    int InD[65] = { 0 };
    vector<int> H[65]; //边集
    bool vis[65][65] = { false };
    int n, m;
    queue<int> myque;
public:
    bool isPrintable(vector<vector<int>>& targetGrid) {
        n = targetGrid.size(), m = targetGrid[0].size();
        for (int i = 1; i <= 60; i++) { //初始化
            top[i] = left[i] = 0x3f3f3f3f;
            bottom[i] = right[i] = -1;
        }
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                int curcolor = targetGrid[i][j];
                bottom[curcolor] = max(bottom[curcolor], i); //最大下边界
                right[curcolor] = max(right[curcolor], j); //以下同理
                top[curcolor] = min(top[curcolor], i);
                left[curcolor] = min(left[curcolor], j);
            }
        } //确定所有颜色的上下左右边界
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                int curcolor = targetGrid[i][j];
                for (int faColor = 1; faColor <= 60; faColor++) {
                    if (faColor == curcolor || vis[faColor][curcolor]) continue; //重边或者重点，跳过。
                    if (top[faColor] <= i && i <= bottom[faColor]
                        && left[faColor] <= j && j <= right[faColor]) { //fa颜色集合包含了curcolor集合
                        H[faColor].push_back(curcolor); //说明两颜色存在偏序关系
                        vis[faColor][curcolor] = true;
                        InD[curcolor]++; 
                    }
                }
                
            }
        }
        /*以下为拓扑排序，通过拓扑排序判断这些偏序关系即有向图，是否存在环，有环说明两颜色集合互相包含*/
        int cnt = 0;
        for (int i = 1; i <= 60; i++)
            if (InD[i] == 0) {
                myque.push(i);
                cnt++;
            }
        while (!myque.empty()) {
            int u = myque.front();
            myque.pop();
            for (int i = 0; i < H[u].size(); i++) {
                int v = H[u][i];
                InD[v]--;
                if (InD[v] == 0) {
                    myque.push(v);
                    cnt++;
                }
            }
        }
        return (cnt == 60);
    }
};
```

