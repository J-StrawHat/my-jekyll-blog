# 1583. 统计不开心的朋友 #模拟 #暴力

## [题目链接](https://leetcode-cn.com/problems/count-unhappy-friends/)

## 题意

有`n`为朋友，对每位朋友`i`，`preference[i]`包含 按亲密度从大到小 的朋友编号。

朋友们会被分为若干对，配对情况由`pairs`数组给出，即`pair[i]={xi, yi}`表示`xi`与`yi`相互配对。

当`x`与`y`相互配对且`u`与`v`相互配对的情况下，如果同时满足下面两个条件，`x`就会**不高兴**：

+ `x`->`u`亲密度 大于 `x`->`y`；
+ `u`->`x`亲密度 大于 `u`->`v`；

## 分析

初看题目，我以为一旦满足上述两条件，不但`x`不高兴，`u`也会不高兴。然而，这是错的。

对于数据`6, preferences=[[1,4,3,2,5],[0,5,4,3,2],[3,0,1,5,4],[2,1,4,0,5],[2,1,0,3,5],[3,4,2,0,1]], pairs = [[3,1],[2,0],[5,4]]`，只有`5`号是高兴的，这是因为`5`号对`4`号亲密度太高(rank=4)，`5`号只能去找`1`（rank=5），然而`1`号对`3`号的亲密度更高(rank=5)，被`3`号锁住，因而`5`是高兴的。

由此，我们不应该遍历`pairs`，而是将`pairs`中的配对情况记录到每个人身上，再遍历每个人，检查是否不开心。

下面代码中`rank[i][j]`代表`i`号对`j`号的亲密度。而`mypair[i]`代表`i`在`pair`中配对的另一个人编号。

```c++
class Solution {
private:
    int rank[505][505], mypair[505];
public:
    bool Judge(int cur, int n){ //判断是否不开心
        int p = mypair[cur];
        for (int i = 0; i < n; i++){
            if(rank[cur][p] < rank[cur][i]
            && rank[i][mypair[i]] < rank[i][cur]) //按照题目要求
                return true;
        }
        return false;
    }
    int unhappyFriends(int n, vector<vector<int>>& preferences, vector<vector<int>>& pairs) {
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n - 1; j++) 
                rank[i][preferences[i][j]] = n - j; //存下i的朋友亲密度
        for (int i = 0; i < n / 2; i ++){ //将每对pair中两人对应队友记录下来
            mypair[pairs[i][0]] = pairs[i][1]; 
            mypair[pairs[i][1]] = pairs[i][0];
        }
        int ans = 0;
        for(int i = 0; i < n; i++) 
            if(Judge(i, n)) ans++;
        return ans;
    }
};
```



# 1585. 检查字符串是否可以通过排序子字符串得到另一个字符串 #暴力 #排序思想 #模拟

## [题目链接](https://leetcode-cn.com/problems/check-if-string-is-transformable-with-substring-sort-operations/solution/jian-cha-zi-fu-chuan-shi-fou-ke-yi-tong-guo-pai-2/)

## 题意

给定字符串`s`和`t`，可通过若干次的下述操作将`s`转化为`t`——选择`s`中一个非空子字符串并将它包含的字符**就地升序排序**。现要你判断`s`通过这些操作能否得到`t`。其中子字符串定义为一个字符串中**连续**的若干字符。

## 分析

更详细的证明过程参考[零神的题解](https://leetcode-cn.com/problems/check-if-string-is-transformable-with-substring-sort-operations/solution/jian-cha-zi-fu-chuan-shi-fou-ke-yi-tong-guo-pai-2/)

模拟`s`到`t`的过程，对`t`串一个个字符进行考虑。首先考虑`t[0]`（`t`串的首字符）在`s`串中首位置，记为`s[t_0]`(=`t[0]`)。我们怎么知道字符`s[t_0]`能否通过排序使其放到`s`首位呢？只有当`s[t_0]`都小于`s[0],s[1],...,s[t_0 - 1]`这些元素，即比`s[t_0]`小的元素 都在`s[t_0]`的右侧，这时候`s[t_0]`有机会通过排序将原来位置降到`s`的首位置。当我们比较完，确定满足该条件时，`s[t_0]`以后不再考虑，转而考虑下一字符`t[1]`，找到在`s`中对应的 最前位置`s[t_i]`，判断`s[t_i]`能否移动到`s`中的第1个位置…..

如何实现？我们为`10`个数字的每个数字都准备一个队列，用来存储该数字在`s`串中的位置。为什么要用队列？因为我们在检查`cur`前面的数字是否在`cur`左侧时，只需要用查询数字`0,1,...,cur-1`的当前一个位置即可，若检查不出，说明可以将`cur`搬到前面，接下来直接将该`cur`的最新下标排除即可，模拟排序子过程。

```c++
class Solution {
public:
    bool isTransformable(string s, string t) {
        vector< queue<int> > pos(10);
        for (int i = 0; i < s.length(); i++)
            pos[s[i] - '0'].push(i); //存下每一种数字在s串中的位置
        for (int i = 0; i < t.length(); i++){ //遍历t串中每一个字符
            int cur = t[i] - '0';
            if(pos[cur].empty()) //说明s串字符数量与t串不相等，显然无法满足题意
                return false;
            for (int j = 0; j <= cur; j++){ //检查s串中 比t[i]小 的字符 所在的下标
                if(!pos[j].empty() && pos[j].front() < pos[cur].front())
                    return false; 
                //如果t串中t[i]的左边，存在一个比t[i]还小的字符，显然不能将t[i]冒泡到前面
            }
            pos[cur].pop(); //即时更新数字cur的下标
        }
        return true;
    }
};
```

