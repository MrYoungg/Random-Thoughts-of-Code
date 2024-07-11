### 十二、1049.最后一块石头的重量Ⅱ

[1049. 最后一块石头的重量 II - 力扣（LeetCode）](https://leetcode.cn/problems/last-stone-weight-ii/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/1049.最后一块石头的重量II.html#算法公开课)

#### 1、思路

#### 2、注意点

1. 分成了两堆石头，dp.back()是其中一堆，最后的结果是两堆石头相减

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

[494. 目标和 - 力扣（LeetCode）](https://leetcode.cn/problems/target-sum/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0494.目标和.html#思路)

#### 1、思路

#### 2、注意点

1. 边界条件1：如果**positiveSum不是整数**，说明数组中的整数不能凑出positiveSum，return 0；
2. 边界条件2：**target的绝对值大于numsSum**，无法实现，return 0；

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

#### 1、思路

#### 2、注意点

#### 3、代码

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

