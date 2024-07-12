### 十二、1049.最后一块石头的重量Ⅱ

> 有一堆石头，每块石头的重量都是正整数。
>
> 每一回合，从中选出任意两块石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：
>
> 如果 x == y，那么两块石头都会被完全粉碎；
>
> 如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。
>
> 最后，最多只会剩下一块石头。返回此石头最小的可能重量。如果没有石头剩下，就返回 0。
>
> 示例：
>
> - 输入：[2,7,4,1,8,1]
> - 输出：1
>
> 解释：
>
> - 组合 2 和 4，得到 2，所以数组转化为 [2,7,1,8,1]，
> - 组合 7 和 8，得到 1，所以数组转化为 [2,1,1,1]，
> - 组合 2 和 1，得到 1，所以数组转化为 [1,1,1]，
> - 组合 1 和 1，得到 0，所以数组转化为 [1]，这就是最优值。

[1049. 最后一块石头的重量 II - 力扣（LeetCode）](https://leetcode.cn/problems/last-stone-weight-ii/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/1049.最后一块石头的重量II.html#算法公开课)

#### 1、思路

1. 将石头尽可能分成相等的两堆，两堆之差就是最后剩下的石头重量，和**十一、416.分割等和子集**是不是很像了！
2. 转换成**零一背包**问题，背包的目标容量target为石头重量总和sum的一半，探究该背包**最多能装多重的石头**；

#### 2、注意点

1. 分成了两堆石头，dp.back()是其中一堆，最后的结果是**两堆石头相减**;

    ```c++
    // 最后剩下的石头是两堆石头之差
    int stones1 = dp.back();
    int stones2 = sum - dp.back();
    int result = stones2 - stones1;
    return result;
    ```

2. **stones1一定小于等于stones2**，因为背包最多装一半重量的石头，不会超过一半；

3. **内层遍历**要从`j = dp.size() - 1`开始，`dp.size()`是越界位置；

#### 3、代码

```c++
class Solution {
public:
    int lastStoneWeightII(vector<int>& stones) {

        // 尽可能将石头分成重量相同的两堆，互相碰撞后得到最小石头或0
        // 转换为01背包问题

        // 求和
        int sum = accumulate(stones.begin(), stones.end(), 0);
        int target = sum / 2; // 背包的目标容量

        // dp[j]含义：容量为j的背包所能装的最大重量
        // dp初始化：全0即可，不影响从后向前遍历时取max
        vector<int> dp(target + 1, 0);

        for (int i = 0; i < stones.size(); i++) {
            for (int j = dp.size() - 1; j >= stones[i]; j--) {

                dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]);
            }
        }

        // 最后剩下的石头是两堆石头之差
        int stones1 = dp.back();
        int stones2 = sum - dp.back();
        // cout << "stones1:" << stones1 << endl;
        // cout << "stones2:" << stones2 << endl;
        
        // stones1一定小于等于stones2，因为背包最多装一半重量的石头，不会超过一半
        int result = stones2 - stones1;
        return result;
    }
};
```

### 十三、494.目标和

> 给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。
>
> 返回可以使最终数组和为目标数 S 的所有添加符号的方法数。
>
> 示例：
>
> - 输入：nums: [1, 1, 1, 1, 1], S: 3
> - 输出：5
>
> 解释：
>
> - -1+1+1+1+1 = 3
> - +1-1+1+1+1 = 3
> - +1+1-1+1+1 = 3
> - +1+1+1-1+1 = 3
> - +1+1+1+1-1 = 3
>
> 一共有5种方法让最终目标和为3。

[494. 目标和 - 力扣（LeetCode）](https://leetcode.cn/problems/target-sum/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0494.目标和.html#思路)

#### 1、思路

1. 列方程求解：

    - positiveSum - negativeSum = target（1）

    - positiveSum + negativeSum = numsSum（2）

    - 联立（1）、（2）解得：**positiveSum = (target + numsSum)/2；**

2. 可以看出positiveSum是固定值，转换成零一背包问题，**用数组中的数填满容量为positiveSum的背包；**

#### 2、注意点

1. 边界条件1：如果**positiveSum不是整数**，说明数组中的数不能凑出positiveSum，return 0；
2. 边界条件2：如果**target的绝对值大于numsSum，如target=-200，numsSum=100**，无法实现，return 0；

#### 3、代码

```c++
class Solution {
public:
    void printVec(const vector<int>& vec) {
        for (auto i : vec) {
            cout << i << '\t';
        }
        cout << endl;
    }

    int findTargetSumWays(vector<int>& nums, int target) {
        int positiveSum = 0; // 正数和
        int negativeSum = 0; // 负数和
        int numsSum = accumulate(nums.begin(), nums.end(), 0);

        // positiveSum - negativeSum = target（1）
        // positiveSum + negativeSum = numsSum（2）
        // 联立（1）、（2）解得:positiveSum = (target + numsSum)/2
        positiveSum = (target + numsSum) / 2;
        // 边界条件1：如果positiveSum不是整数，说明数组中的整数不能凑出positiveSum
        if((target + numsSum) % 2!=0){
            return 0;
        }

        // 边界条件2：target的绝对值大于numsSum，无法实现
        if(abs(target)>numsSum){
            return 0;
        }

        // 抽象成01背包问题，用nums中的元素装满容量为positiveSum的背包共有多少种方法？
        // dp[capacity]含义：装满容量为capacity的背包有dp[capacity]种方法
        vector<int> dp(positiveSum + 1);

        // 初始化：dp[0]初始化为1，用于递推，否则后续所有递推和都是0
        dp[0] = 1;

        for (int i = 0; i < nums.size(); i++) {
            for (int capacity = dp.size() - 1; capacity >= nums[i];
                 capacity--) {
                // 递推公式：不同路径的方法逐个叠加
                dp[capacity] += dp[capacity - nums[i]];
            }
            // printVec(dp);
        }

        return dp.back();
    }
};
```

### 十四、474.一和零

> 给你一个二进制字符串数组 strs 和两个整数 m 和 n 。
>
> 请你找出并返回 strs 的最大子集的大小，该子集中 最多 有 m 个 0 和 n 个 1 。
>
> 如果 x 的所有元素也是 y 的元素，集合 x 是集合 y 的 子集 。
>
> 示例 1：
>
> - 输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3
> - 输出：4
> - 解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。 其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3

[474. 一和零 - 力扣（LeetCode）](https://leetcode.cn/problems/ones-and-zeroes/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0474.一和零.html#算法公开课)

#### 1、思路

1. 转换成**零一背包**问题，装满容量为m个0，n个1的背包，最多能装多少个物品；
2. `dp[i][j]`含义：装满容量为i个0，j个1的背包，最多能装`dp[i][j]`个物品；
3. 递推公式：`dp[i][j]=max(dp[i][j],dp[i-zeroNumber][j-oneNumber]+1);`
    - `dp[i][j]`：不装当前字符串的原字符串数；
    - `dp[i-zeroNumber][j-oneNumber]+1`：装入当前字符串之后的字符串数；

#### 2、代码

```c++
class Solution {
public:
    int getZero(string& str) {
        int numberOfZero = 0;
        for (char c : str) {
            if (c == '0') {
                numberOfZero++;
            }
        }
        return numberOfZero;
    }

    int getOne(string& str) {
        int numberOfOne = 0;
        for (char c : str) {
            if (c == '1') {
                numberOfOne++;
            }
        }
        return numberOfOne;
    }

    int findMaxForm(vector<string>& strs, int m, int n) {
        // 转换成01背包问题，装满容量为m个0，n个1的背包，最多能装多少物品
        // dp[i][j]含义：装满容量为i个0，j个1的背包，最多能装dp[i][j]个物品
        // 初始化：全0即可
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (string str : strs) {
            int zeroNumber = getZero(str);
            int oneNumber = getOne(str);

            for(int i=m; i>=zeroNumber; i--){
                for(int j=n;j>=oneNumber;j--){
                    dp[i][j]=max(dp[i][j],dp[i-zeroNumber][j-oneNumber]+1);
                }
            }
        }

        return dp[m][n];
    }
};
```
