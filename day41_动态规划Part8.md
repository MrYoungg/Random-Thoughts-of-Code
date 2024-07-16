### 二十七、121.买卖股票的最佳时机

> 给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。
>
> 你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。
>
> 返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。
>
> - 示例 1：
> - 输入：[7,1,5,3,6,4]
> - 输出：5
>     解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。

[121. 买卖股票的最佳时机 - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0121.买卖股票的最佳时机.html#思路)

#### 1、思路

1. **`dp[i][j]`含义**

    - `dp[i][0]`：在第i天持有股票时，手中的最大金钱（事实上就是在第i天之前买股票所消耗的最少金钱）
    - `dp[i][1]`：在第i天不持有股票，手中的最大金钱；

2. **递推公式**

    - `dp[i][0]`：即第i天持有股票，有两种可能

        - 从第i-1天持有至第i天（`dp[i - 1][0]`）;
        - 恰好在第i天买入（`-prices[i]`，注意只能买入一次）；

        ``````c++
        dp[i][0] = max(dp[i - 1][0], -prices[i]); // 取较大值
        ``````

    - `dp[i][1]`：若第i天不持有股票，也有两种可能

        - 从第i-1天就不持有（`dp[i - 1][1]`）；

        - 第i-1天仍持有，在第i天卖出（`dp[i - 1][0] + prices[i]`）；

            ```c++
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]); // 取较大值
            ```

3. **初始化**

    - 从dp[0]开始往后推导，需要初始dp[0]；

        ```c++
        // 初始化
        dp[0][0] = -prices[0]; // 持有即花费金钱买入
        dp[0][1] = 0; // 不持有即手中金钱仍未0
        ```

        <img src="./day41_动态规划Part8.assets/20210224225642465.png" alt="121.买卖股票的最佳时机" style="zoom: 67%;" />

#### 2、注意点

- 第i天持有股票的情况（即`dp[i][0]`）的递推公式需要特别注意，由于仅能买入一次股票，所以在递推公式中考虑**买入情况**时应该直接为`-prices[i]`；

#### 3、代码

```c++
class Solution {
public:
    void printVec(vector<vector<int>>& dp) {
        for (auto i : dp) {
            cout << i[0] << " " << i[1] << endl;
        }
    }

    int maxProfit(vector<int>& prices) {
        // dp[i][j]含义
        // dp[i][0]：在第i天持有股票时，手中的最大金钱（事实上就是在第i天之前买股票所消耗的最少金钱）
        // dp[i][1]：在第i天不持有股票，手中的最大金钱
        vector<vector<int>> dp(prices.size(), vector<int>(2));

        // 初始化
        dp[0][0] = -prices[0];
        dp[0][1] = 0;

        for (int i = 1; i < prices.size(); i++) {
            // 递推公式
            // dp[i][0]：若第i天持有股票，有两种可能
            // 1、从第i-1天持有至第i天
            // 2、恰好在第i天买入(注意只能买入一次)
            dp[i][0] = max(dp[i - 1][0], -prices[i]); // 取较大值

            // dp[i][1]：若第i天不持有股票，也有两种可能
            // 1、从第i-1天就不持有
            // 2、第i-1天持有，在第i天卖出
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]); // 取较大值
        }
        // printVec(dp);
        return dp.back()[1];
    }
};
```

### 二十八、122.买卖股票的最佳时机II

> 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> - 示例 1:
> - 输入: [7,1,5,3,6,4]
> - 输出: 7
>     解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4。随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。

[122. 买卖股票的最佳时机 II - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/submissions/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0122.买卖股票的最佳时机II（动态规划）.html#算法公开课)

#### 1、思路

1. 与**二十七、121.买卖股票的最佳时机**的区别在于，本题能够无限次买卖股票；
2. 因此递推公式有变化，考虑在第i天持有股票的情况（`dp[i][0]`），第2种可能不再是**“首次买入股票”**，应该变为：**第i-1天不持有股票（可能之前已经卖过），而第i天买入股票；**
    ` dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]); // 取较大值`

#### 2、代码

```c++
class Solution {
public:
    void printVec(vector<vector<int>>& dp) {
        for (auto i : dp) {
            cout << i[0] << " " << i[1] << endl;
        }
    }

    int maxProfit(vector<int>& prices) {
        // dp[i][j]含义
        // dp[i][0]：在第i天持有股票时，手中的最大金钱（事实上就是在第i天之前买股票所消耗的最少金钱）
        // dp[i][1]：在第i天不持有股票，手中的最大金钱
        vector<vector<int>> dp(prices.size(), vector<int>(2));

        // 初始化
        dp[0][0] = -prices[0];
        dp[0][1] = 0;

        for (int i = 1; i < prices.size(); i++) {
            // 递推公式
            // dp[i][0]：若第i天持有股票，有两种可能
            // 1、从第i-1天持有至第i天
            // 2、第i-1天不持有,第i天买入(注意可以买卖多次)
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]); // 取较大值

            // dp[i][1]：若第i天不持有股票，也有两种可能
            // 1、从第i-1天就不持有
            // 2、第i-1天持有，在第i天卖出
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]); // 取较大值
        }
        // printVec(dp);
        return dp.back()[1];
    }
};
```

### 二十九、123.买卖股票的最佳时机

> 给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。
>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> - 示例 1:
> - 输入：prices = [3,3,5,0,0,3,1,4]
> - 输出：6 解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3。

[123. 买卖股票的最佳时机 III - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0123.买卖股票的最佳时机III.html#思路)

#### 1、思路

1. **dp含义：**

    - `dp[i][0]`:第一次持有
    - `dp[i][1]`:第一次不持有
    - `dp[i][2]`:第二次持有
    - `dp[i][3]`:第二次不持有

2. **递推**

    ``` c++
    dp[i][0] = max(dp[i - 1][0], -prices[i]);
    dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
    dp[i][2] = max(dp[i - 1][2], dp[i - 1][1] - prices[i]);
    dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] + prices[i]);
    ```

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        
        // dp[i][0]:第一次持有
        // dp[i][1]:第一次不持有
        // dp[i][2]:第二次持有
        // dp[i][3]:第二次不持有
        vector < vector<int>> dp(prices.size(), vector<int>(4,0));

        dp[0][0] = -prices[0];
        dp[0][1] = 0;
        dp[0][2] = -prices[0];
        dp[0][3] = 0;

        for (int i = 1; i < prices.size(); i++) {
            dp[i][0] = max(dp[i - 1][0], -prices[i]);
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
            dp[i][2] = max(dp[i - 1][2], dp[i - 1][1] - prices[i]);
            dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] + prices[i]);
        }

        return max(dp.back()[1],dp.back()[3]);
    }
};
```

