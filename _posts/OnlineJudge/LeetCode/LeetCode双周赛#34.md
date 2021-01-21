# LeetCode双周赛#34

# 5492. 分割字符串的方案数 #组合公式 #乘法原理  #区间分割

##  [题目链接](https://leetcode-cn.com/problems/number-of-ways-to-split-a-string/)

## 题意

给定`01`二进制串$s$，可将$s$分割为**三个非空** 字符串$s_1,s_2,s_3$，即($s_1+s_2+s_3=s$)。现要你求出分割$s$的方案数，保证$s_1,s_2,s_3$中字符`1`的数目相同（对$1e9+7$取模），他们的长度不一定相等

## 分析

举个例子，$01100011000101$串，可以知道三个子串必须包含2个`'1'`，我们观察到左子串的界限，可以如下划分：$011|00011000101$、$0110|0011000101$、$01100|011000101$、$011000|11000101$，共四种划分方案；同理对于右子串，共有四种划分方案。当我们将左右子串由于中间子串的间隔，使得左右子串的确定不会相互影响，同时中间子串也相应确定下来，通过乘法原理，总方案数=左子串划分方案数$\times$右子串划分方案数=16。

然而，别忘了诸如$00000$等只包含`'0'`的串，三子串的划分确定会相互影响，比如$0|000|0$、$000|0|0$等，此时我们可以用高中所学排列组合的“挡板法”确定下来，即串长为$n$，有$n-1$空位，可以插入两个挡板，即$C^2_{n-1}$。

```c++
typedef long long ll;
class Solution {
private:
    int sum[100005];
    const int MOD = 1e9 + 7;
public:
    int numWays(string s) {
        int n = s.size();
        for (int i = 0; i < n; i++) //利用前缀和统计1的数目
            sum[i + 1] = sum[i + 1 - 1] + (s[i] == '1');
        if(sum[n] == 0){ //特判全部为0的串
            ll ans = (ll)(n - 1) * (ll)(n - 2)  / (ll)2;
            return ans % MOD;
        }
        if(sum[n] % 3 != 0) return 0; //特判不含有3的整数倍的串，无法分割
        else{
            int diff = sum[n] / 3; //确定每个子串中的'1'数量
            ll a = 0, b = 0;
            for (int i = 1; i <= n - 2; i++) //确定左子串可拓展的长度
                if(sum[i] == diff) a++;
            for (int i = n; i >= 2; i--) //确定右子串可拓展的长度
                if(sum[n] - sum[i - 1] == diff) b++;
            ll ans = (a % MOD) * (b % MOD); //乘法原理
            return ans % MOD;
        }
    }
};
```

# 5493. 删除最短的子数组使剩余数组有序 #归并排序 #区间分割

## [题目链接](https://leetcode-cn.com/problems/shortest-subarray-to-be-removed-to-make-array-sorted/)

## 题意

给定整数数组`arr` ，你需要删除**一个**子数组（原数组中**连续**的一个子序列，可以为空），使得`arr`中剩下的元素是 **非递减**的。现要你求出满足题目要求的最短子数组的长度。

## 分析

感谢[@Liuyh](https://leetcode-cn.com/u/liuyh/)思路。

题目要求只能删除一个子数组，也就说子数组内部存在一小段是非递减的，但也必须要删去。一般地，我们一定是从数组中间部分进行删除（只删除左部分或只删除右部分属于退化情况），即`[....|XXXX|....]`（X表示要删除的部分）。 删除中间的子数组(并不代表最终删除子数组)，意味着剩余前后两个子数组都符合非递减，这一操作需要前后两个指针，分别将两数组边界确定下来。代码中左子数组为`[0, lo]`，而右子数组为`[hi, len-1]`。比如原数组`[1,2,3,10,4,2,3,5]`，确定出左子数组` [1,2,3,10]`，`lo`指向`10`，右子数组`[2, 3, 5]`。中间数组删除长度为$hi-lo+1$

现在我们要合并上述两个子数组，需要用到归并排序的思想，一旦发现右子数组比左子数组还大的元素，其中一个元素给舍弃掉（无需考虑是删除左数组的还是右数组的）。举个例子：

+ `[1,2,3,10]`、`[2,3,5]` 
+ 由于`1`<`2`，此时`[2,3,10]`、`[2,3,5]`；
+ 由于`2`<=`2`，此时`[3,10]`、`[2,3,5]`
+ 由于`3`>`2`，舍弃右子数组的`2`，左子数组跳过`3`,`ans++`，此时`[10]`、`[3,5]`
+ 由于`10`>`3`，舍弃`10`，得到` []`、`[5]`，算法结束。

```c++
class Solution {
public:
    int findLengthOfShortestSubarray(vector<int>& arr) {
        int lo = 0, len = arr.size(), hi = arr.size() - 1;
        if(len == 1) return 0; //特判：一个元素就无需删除了
        while(lo + 1 < len && arr[lo] <= arr[lo + 1]) lo++;
        //开始分割，确定左区间的右端点，注意，是往后递推，这样能够保证lo最终指向的依然是非降左子序列的右端点
        if(lo + 1 == len) return 0; //说明原数组是个非降序列，直接返回0
        while(lo < hi &&  arr[hi - 1] <= arr[hi]) hi--;
        if(hi == lo && hi == 0) return len - 1; //说明原数组是完全递减区间

        int ans = hi - lo - 1; //统计分割出来的区间
        int i = 0, j = hi; 

        while(i <= lo && j < len){ //合并区间
            if(arr[i] <= arr[j]) i++; //以左序列为基准
            else if(arr[i] > arr[j]){ //因为题目要求从左至右递增
                i++; j++;//注意!一定是两个指针同时前进，因为你不知道哪个子数组影响最终结果
                ans++;
            }
        }
        return ans;
    }
};
```

# 5494. 统计所有可行路径 #记忆化搜索

## [题目链接](https://leetcode-cn.com/problems/count-all-possible-routes/)

## 题意

有若干个城市，第`i`个城市有`location[i]`值。给定你城市出发起点`start`、目的地城市`finish`、初始汽油总量`fuel`。每当你从城市`j`移动到城市`i`时，消耗的油量为`|location[j] - location[i]|`。你要保证剩余的油量非负，方能移动到其他城市。同时，你可以经过任意城市**超过一次**（包括`start`和`finish`）。现要你求出从`start`到`finish`可能路径的数目(需取模)

## 分析

观察到数据范围$fuel\leq200$及$locations.len\leq100$，直接记忆化搜索即可。但注意，尽管你此时到达了`finish`城市，只要你油量足够，仍能够移动到其他城市。

`dp[i][j]`表示**当前**起点为`i`，剩余油量`j`的总方案数。

要注意，我直接把`dp`数组放到全局变量的时候，出锅了不少qaq，别忘了在类内要对这个全局变量初始化一下，同时记忆化搜索一般赋值为`-1`较为稳妥。

```c++
typedef long long ll;
int dp[105][205];
const int MOD = 1e9 + 7;
class Solution {
public:
    int dfs(vector<int>& locations, int start, int finish, int fuel){
        if(dp[start][fuel] != -1) return dp[start][fuel];
        ll ans = 0; int len = locations.size();
        if(start == finish) ans++;
        for (int i = 0; i < len; i++){
            int diff = abs(locations[i] - locations[start]);
            if(start == i) continue;
            if(fuel - diff >= 0){
                ans = (ans + dfs(locations, i, finish, fuel - diff)) % MOD;
            }
        }
        dp[start][fuel] = (ans % MOD);
        return dp[start][fuel];
    }
    int countRoutes(vector<int>& locations, int start, int finish, int fuel) {
        memset(dp, -1, sizeof(dp));
        return dfs(locations, start, finish, fuel);
    }
};
```





