### 三十五、1143.最长公共子序列

> 给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列的长度。
>
> 一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
>
> 例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。
>
> 若这两个字符串没有公共子序列，则返回 0。
>
> 示例 1:
>
> - 输入：text1 = "abcde", text2 = "ace"
> - 输出：3
> - 解释：最长公共子序列是 "ace"，它的长度为 3。

[1143. 最长公共子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-common-subsequence/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/1143.最长公共子序列.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {

        // dp[i][j]：包含text1[0:i-1]和text2[0:j-1]的序列中，最长公共子序列的长度
        // 初始化：0行0列必须初始为0，其余会被覆盖，为了方便初始化为0即可
        vector<vector<int>> dp(text1.size() + 1,
                               vector<int>(text2.size() + 1, 0));

        for (int i = 1; i < dp.size(); i++) {
            for (int j = 1; j < dp[0].size(); j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    // 遇到不相等的字符，则将两个字符串分别退1，取较大值
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp.back().back();
    }
};
```

### 三十六、1035.不相交的线

> 我们在两条独立的水平线上按给定的顺序写下 A 和 B 中的整数。
>
> 现在，我们可以绘制一些连接两个数字 A[i] 和 B[j] 的直线，只要 A[i] == B[j]，且我们绘制的直线不与任何其他连线（非水平线）相交。
>
> 以这种方法绘制线条，并返回我们可以绘制的最大连线数。
>
> <img src="./day44_动态规划Part11.assets/2021032116363533.png" alt="1035.不相交的线" style="zoom: 50%;" />

[1035. 不相交的线 - 力扣（LeetCode）](https://leetcode.cn/problems/uncrossed-lines/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/1035.不相交的线.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    int maxUncrossedLines(vector<int>& nums1, vector<int>& nums2) {
        // 就是求最长相等子序列的长度
        vector<vector<int>> dp(nums1.size() + 1,
                               vector<int>(nums2.size() + 1, 0));

        for (int i = 1; i < dp.size(); i++) {
            for (int j = 1; j < dp[0].size(); j++) {
                if (nums1[i - 1] == nums2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp.back().back();
    }
};
```

### 三十七、53. 最大子数组和

> 给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
>
> 示例:
>
> - 输入: [-2,1,-3,4,-1,2,1,-5,4]
> - 输出: 6
> - 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

[53. 最大子数组和 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-subarray/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0053.最大子序和（动态规划）.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        
        
        // dp[i]:nums[0:i]之间的最大连续子数组和；
        vector<int> dp(nums.size());

        dp[0]=nums[0];
        int result=nums[0];
        
        for(int i=1;i<dp.size();i++){
            // 递推公式：
            // 1、将nums[i]加入前面连续数组的最大和中
            // 2、前面连续的数组和会拖累nums[i]（dp[i-1]为负），直接从nums[i]开始重新求和
            dp[i]=max(dp[i-1]+nums[i],nums[i]);
            result=max(result,dp[i]);
        }

        return result;

    }
};
```

### 三十八、392.判断子序列

> 给定字符串 s 和 t ，判断 s 是否为 t 的子序列。
>
> 字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。
>
> 示例 1：
>
> - 输入：s = "abc", t = "ahbgdc"
> - 输出：true
>
> 示例 2：
>
> - 输入：s = "axc", t = "ahbgdc"
> - 输出：false

[392. 判断子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/is-subsequence/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0392.判断子序列.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        // dp[i][j]：包含s[0:i-1]和t[0:j-1]的序列中，最长公共子序列的长度
        // 初始化：0行0列必须初始为0，其余会被覆盖，为了方便初始化为0即可
        vector<vector<int>> dp(s.size() + 1,
                               vector<int>(t.size() + 1, 0));

        for (int i = 1; i < dp.size(); i++) {
            for (int j = 1; j < dp[0].size(); j++) {
                if (s[i - 1] == t[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    // 遇到不相等的字符，则将两个字符串分别退1，取较大值
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return (dp.back().back()==s.size())?true:false;
    }
};
```

