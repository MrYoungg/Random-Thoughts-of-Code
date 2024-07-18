### 三十二、300.最长递增子序列

> 给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。
>
> **子序列** 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列
>
> **示例 1：**
>
> ```
> 输入：nums = [10,9,2,5,3,7,101,18]
> 输出：4
> 解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
> ```

[300. 最长递增子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-increasing-subsequence/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0300.最长上升子序列.html#思路)

#### 1、思路

1. dp[i]：以nums[i]为序列结尾的最长递增子序列长度；

2. 递推公式

    - 两层for循环，外层遍历每个元素，内层遍历该元素之前的所有元素；

    - ```c++
        for (int i = 1; i < nums.size(); i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                    
                }
            }
        }
        ```

3. 初始化：全部初始化为1，因为至少包含当前nums[i]；

#### 2、注意点

- result存储结果，必须初始化为1，否则遇到长度为1的数组时会出错；

#### 3、代码

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        // 存储结果，必须初始化为1
        int result=1;

        // dp[i]:以nums[i]为序列结尾的最长递增子序列长度；
        // 初始化为1，因为至少包含当前nums[i]
        vector<int> dp(nums.size(), 1);


        for (int i = 1; i < nums.size(); i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                    
                }
            }
            result=max(result,dp[i]);
        }

        return result;
    }
};
```

### 三十三、674.最长连续递增序列

> 给定一个未经排序的整数数组，找到最长且 连续递增的子序列，并返回该序列的长度。
>
> 连续递增的子序列 可以由两个下标 l 和 r（l < r）确定，如果对于每个 l <= i < r，都有 nums[i] < nums[i + 1] ，那么子序列 [nums[l], nums[l + 1], ..., nums[r - 1], nums[r]] 就是连续递增子序列。
>
> 示例 1：
>
> - 输入：nums = [1,3,5,4,7]
> - 输出：3
> - 解释：最长连续递增序列是 [1,3,5], 长度为3。尽管 [1,3,5,7] 也是升序的子序列, 但它不是连续的，因为 5 和 7 在原数组里被 4 隔开。

[674. 最长连续递增序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-continuous-increasing-subsequence/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0674.最长连续递增序列.html#算法公开课)

#### 1、思路

1. **直接思路（贪心）**
    1. 遍历数组，遇到nums[i]>nums[i-1]时则count++，否则count复位为1；
    2. result记录count的最大值；
2. **动态规划**
    1. dp[i]：以nums[i]为序列结尾的最长连续递增子序列的长度；
    2. 递推公式：`if(nums[i]>nums[i-1]){dp[i]=dp[i-1]+1};`
    3. 初始化：全为1；

#### 2、代码

##### （1）贪心

```c++
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {
        int result=1;
        int count=1;

        for(int i=1;i<nums.size();i++){
            if(nums[i]>nums[i-1]){
                count++;
            }
            else{
                count=1;
            }
            result=max(result,count);
        }

        return result;
    }
};
```

##### （2）动态规划

```c++
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {
        
        int result=1;
        
        // dp[i]:以nums[i]为序列结尾的最长连续递增子序列的长度
        vector<int> dp(nums.size(),1);

        for(int i=1;i<nums.size();i++){
            if(nums[i]>nums[i-1]){
                dp[i]=dp[i-1]+1;
            }
            result=max(result,dp[i]);
        }

        return result;
    }
}; 
```

### 三十四、718.最长重复子数组

> 给两个整数数组 A 和 B ，返回两个数组中公共的、长度最长的子数组的长度。
>
> 示例：
>
> 输入：
>
> - A: [1,2,3,2,1]
> - B: [3,2,1,4,7]
> - 输出：3
> - 解释：长度最长的公共子数组是 [3, 2, 1] 。

[718. 最长重复子数组 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-length-of-repeated-subarray/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0718.最长重复子数组.html#算法公开课)

#### 1、思路

1. `dp[i][j]`：以**nums1[i-1]**和**nums2[j-1]**为结尾的最长重复子序列；

    - 用`[i][j]`表示i-1和j-1可以使得二维数组的第0行和第0列无意义，直接初始化为0，从（1,1）开始遍历，且每一项dp都可以通过递推公式推出；
    - 如果用`dp[i][j]`表示以**nums1[i]**和**nums2[j]**为结尾的最长重复子序列，则`dp[0][j]`和`dp[i][0]`需要另外初始化；

2. 递推公式

    - 两层for循环，外层遍历nums1，内层遍历nums2；

    - ```c++
        for (int i = 1; i <= nums1.size(); i++) {
            for (int j = 1; j <= nums2.size(); j++) {
                if (nums1[i - 1] == nums2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                result=max(result,dp[i][j]);
            }
        }
        ```

3. 初始化：初始化为0，因为未判断时相等子序列长度应该为0；

#### 2、注意点

- 注意dp的行列大小应该定义为size+1；

#### 3、代码

```c++
class Solution {
public:
    int findLength(vector<int>& nums1, vector<int>& nums2) {
        int result=0;

        // dp[i][j]:以nums1[i-1]和nums2[j-1]为结尾的最长重复子序列
        // 注意dp的行列大小应该定义为size+1
        // 初始化:第0行和第0列无意义,初始化为0即可
        vector<vector<int>> dp(nums1.size()+1, vector<int>(nums2.size()+1, 0));

        for (int i = 1; i <= nums1.size(); i++) {
            for (int j = 1; j <= nums2.size(); j++) {
                if (nums1[i - 1] == nums2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                result=max(result,dp[i][j]);
            }
        }

        return result;
    }
};
```

