### 三十、188.买卖股票的最佳时机IV

> 给定一个整数数组 prices ，它的第 i 个元素 prices[i] 是一支给定的股票在第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。
>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> - 示例 1：
> - 输入：k = 2, prices = [2,4,1]
> - 输出：2 解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2。
> - 

[188. 买卖股票的最佳时机 IV - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0188.买卖股票的最佳时机IV.html#算法公开课)

#### 1、思路

- 可以交易k次，代表每天有2k种状态：第1次持有、第1次不持有、第2次持有...

#### 2、注意点

- 每一天的第一次持有（`dp[i][0]`）不应该初始化为-prices[i]，因为有可能从前一天继承；

#### 3、代码

```c++
class Solution {
public:
    void printVec(vector<vector<int>>& dp) {
        for (int i = 0; i < dp.size(); i++) {
            for (int j = 0; j < dp[0].size(); j++) {
                cout << dp[i][j] << " ";
            }
            cout << endl;
        }
    }
    int maxProfit(int k, vector<int>& prices) {
        // 可以买卖k次，则每天有2k种状态（第1次持有、第1次不持有、第2次持有...）
        vector<vector<int>> dp(prices.size(), vector<int>(2 * k, 0));

        // 每一天的第一次持有不应该初始化为当天股票价格的负数，因为有可能从前一天继承
        // for (int i = 0; i < dp.size(); i++) {
        //     dp[i][0] = -prices[i];
        // }
        
        // 初始化
        for (int j = 0; j < dp[0].size() - 1; j += 2) {
            dp[0][j] = -prices[0];
        }

        // printVec(dp);
        // cout<<endl;

        for (int i = 1; i < dp.size(); i++) {
            for (int j = 0; j < dp[0].size(); j++) {
                // 第一次持有
                if (j == 0) {
                    dp[i][j] = max(dp[i - 1][j], -prices[i]);
                }
                // 非第一次持有
                else if (j % 2 == 0) {
                    dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - 1] - prices[i]);
                }
                // 不持有
                else if (j % 2 == 1) {
                    dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - 1] + prices[i]);
                }
            }
        }
        // printVec(dp);

        return dp.back().back();
    }
};
```

### 三十一、309.最佳买卖股票时机含冷冻期

> 给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。
>
> 设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:
>
> - 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
> - 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。
>
> 示例:
>
> - 输入: [1,2,3,0,2]
> - 输出: 3
> - 解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]

[309. 买卖股票的最佳时机含冷冻期 - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0309.最佳买卖股票时机含冷冻期.html#算法公开课)

#### 1、思路

1. 第i天的状态拓展为四种

    1. `dp[i][0]`：持有股票；
    2. `dp[i][1]`：冷冻期过后还未买入;
    3. `dp[i][2]`：卖出股票；
    4. `dp[i][3]`：为冷冻期；

2. 初始化

    ```c++
    dp[0][0] = -prices[0]; // 第0天买入股票
    dp[0][1] = 0; // 无意义，根据递推公式的需要初始化即可
    dp[0][2] = 0; // 第0天买入又卖出
    dp[0][3] = 0; // 无意义，根据递推公式的需要初始化即可
    ```

#### 2、注意点

- max()库函数仅能传入两个参数，求三个数的最大值需要内嵌调用：`max(a, max(b, c))`

#### 3、代码

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        // dp[i][0]：第i天持有股票
        // dp[i][1]：冷冻期过后还未买入
        // dp[i][2]：第i天卖出股票
        // dp[i][3]：第i天为冷冻期
        vector<vector<int>> dp(prices.size(), vector<int>(4,0));

        // 初始化
        dp[0][0] = -prices[0]; // 第0天买入股票
        dp[0][1] = 0; // 无意义，根据递推公式的需要初始化即可
        dp[0][2] = 0; // 第0天买入又卖出
        dp[0][3] = 0; // 无意义，根据递推公式的需要初始化即可

        for (int i = 1; i < prices.size(); i++) {
            dp[i][0] = max(dp[i - 1][0], max(dp[i - 1][1] - prices[i],
                           dp[i - 1][3] - prices[i]));
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][3]);
            dp[i][2] = dp[i - 1][0] + prices[i];
            dp[i][3] = dp[i - 1][2];
        }

        return max(dp.back()[1], max(dp.back()[2], dp.back()[3]));
    }
};
```

### 三十一、714.买卖股票的最佳时机含手续费

> 给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。
>
> 你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。
>
> 返回获得利润的最大值。
>
> 注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。
>
> 示例 1:
>
> - 输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
> - 输出: 8
>
> 解释: 能够达到的最大利润:
>
> - 在此处买入 prices[0] = 1
> - 在此处卖出 prices[3] = 8
> - 在此处买入 prices[4] = 4
> - 在此处卖出 prices[5] = 9
> - 总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

[714. 买卖股票的最佳时机含手续费 - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0714.买卖股票的最佳时机含手续费（动态规划）.html#思路)

#### 1、思路

- 在每次卖出时扣除手续费即可；

#### 2、注意点

- 最后取持有股票和不持有股票的最大值

#### 3、代码

```c++
class Solution {
public:
    void printVec(vector<vector<int>> &dp){
        for(auto i:dp){
            cout<<i[0]<<" "<<i[1]<<endl;
        }
    }
    int maxProfit(vector<int>& prices, int fee) {
        // dp[i][0]:持有股票
        // dp[i][1]:不持有股票
        vector<vector<int>> dp(prices.size(), vector<int>(2, 0));

        // 初始化
        dp[0][0] = -prices[0];
        dp[0][1] = 0;

        for (int i = 1; i < prices.size(); i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i] -fee);
        }
        // printVec(dp);
        
        return max(dp.back()[1],dp.back()[0]);
    }
};
```

