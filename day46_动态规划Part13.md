### 四十一、647. 回文子串

> 给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。
>
> 具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。
>
> 示例 1：
>
> - 输入："abc"
> - 输出：3
> - 解释：三个回文子串: "a", "b", "c"
>
> 示例 2：
>
> - 输入："aaa"
> - 输出：6
> - 解释：6个回文子串: "a", "a", "a", "aa", "aa", "aaa"

[647. 回文子串 - 力扣（LeetCode）](https://leetcode.cn/problems/palindromic-substrings/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0647.回文子串.html#算法公开课)

#### 1、思路

1. `dp[i][j]`:字符串s[i:j]是不是回文串（true/false）;

2. **初始化**：全为false；

3. **递推公式**

    ```c++
    if (s[i] == s[j]) { // 若两字母相等
        // 子串s[i,j]的长度为1或2时，例如a和aa，必然是回文串
        if (j - i <= 1) {
            result++;
            dp[i][j] = true;
        }
        // 子串s[i,j]长度大于2时，则取决于子串s[i+1:j-1]是不是回文串
        else if (dp[i + 1][j - 1]==true) {
            result++;
            dp[i][j] = true;
        }
    }
    // 若两字母不相同，必然不是回文串，保持初始化的false即可
    ```

4. **遍历顺序**：由于递推公式中`dp[i][j]`取决于`dp[i + 1][j - 1]`（左下角元素），因此需要从下往上，从左往右遍历；
    <img src="./新建 Markdown (2).assets/20210121171032473-20230310132134822.jpg" alt="647.回文子串" style="zoom: 50%;" />

#### 2、注意点

- j小于i时没有意义，无需遍历，因此从j=i开始遍历，最终只需要遍历**右上半部分**dp数组即可；

#### 3、代码

```c++
class Solution {
public:
    int countSubstrings(string s) {
        int result = 0;

        // dp[i][j]:字符串s[i:j]是不是回文串（true/false）
        // 初始化：全为false
        vector<vector<bool>> dp(s.size(), vector<bool>(s.size(), false));

        for (int i = dp.size() - 1; i >= 0; i--) {
            // j小于i时没有意义，无需遍历，因此从j=i开始遍历
            for (int j = i; j < dp[0].size(); j++) {
                if (s[i] == s[j]) { // 若两字母相等
                    // 子串s[i,j]的长度为1或2时，例如a和aa，必然是回文串
                    if (j - i <= 1) {
                        result++;
                        dp[i][j] = true;
                    }
                    // 子串s[i,j]长度大于2时，则取决于子串s[i+1:j-1]是不是回文串
                    else if (dp[i + 1][j - 1]==true) {
                        result++;
                        dp[i][j] = true;
                    }
                }
                // 若两字母不相同，必然不是回文串，保持初始化的false即可
            }
        }

        return result;
    }
};
```

### 四十二、最长回文子序列

> 给定一个字符串 s ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 s 的最大长度为 1000 。
>
> - 示例 1: 输入: "bbbab" 输出: 4 一个可能的最长回文子序列为 "bbbb"。
>
> - 示例 2: 输入:"cbbd" 输出: 2 一个可能的最长回文子序列为 "bb"。

[516. 最长回文子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-palindromic-subsequence/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0516.最长回文子序列.html#算法公开课)

#### 1、思路

1. `dp[i][j]`:在子串s[i:j]中，最长回文子序列的长度为`dp[i][j]`；

2. **初始化**：全0即可；

3. **递推公式**

    ```c++
    // 若两字符相等
    if (s[i] == s[j]) {
        // 若子串s[i:j]长度小于等于2，则子串s[i:j]必为回文串，长度为字符串长度：j-i+1
        if (j - i <= 1) {
            dp[i][j] = j - i + 1;
        }
        // 若子串s[i:j]长度大于2，则最长回文子序列取决于s[i + 1:j -
        // 1]内的最长回文子序列
        else {
            dp[i][j] = dp[i + 1][j - 1] + 2;
        }
    }
    // 若两字符不相等，说明同时两字符同时加入子串中无法构成回文子串，因此考虑仅添加一个
    else {
        // dp[i][j - 1]:仅添加s[i]
        // dp[i+1][j]:仅添加s[j]
        dp[i][j] = max(dp[i][j - 1], dp[i + 1][j]);
    }
    ```

4. 遍历顺序：由于`dp[i][j]`取决于左、下，左下角三个元素，因此从下至上，从左至右遍历；
    <img src="./新建 Markdown (2).assets/20230102172155.png" alt="img" style="zoom:50%;" />

#### 2、代码

```c++
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        // dp[i][j]:在子串s[i:j]中，最长回文子序列的长度为dp[i][j];
        vector<vector<int>> dp(s.size(), vector<int>(s.size(), 0));

        for (int i = dp.size() - 1; i >= 0; i--) {
            for (int j = i; j < dp[0].size(); j++) {
                // 若两字符相等
                if (s[i] == s[j]) {
                    // 若子串s[i:j]长度小于等于2，则子串s[i:j]必为回文串，长度为字符串长度：j-i+1
                    if (j - i <= 1) {
                        dp[i][j] = j - i + 1;
                    }
                    // 若子串s[i:j]长度大于2，则最长回文子序列取决于s[i + 1:j -
                    // 1]内的最长回文子序列
                    else {
                        dp[i][j] = dp[i + 1][j - 1] + 2;
                    }
                }
                // 若两字符不相等，说明同时两字符同时加入子串中无法构成回文子串，因此考虑仅添加一个
                else {
                    // dp[i][j - 1]:仅添加s[i]
                    // dp[i+1][j]:仅添加s[j]
                    dp[i][j] = max(dp[i][j - 1], dp[i + 1][j]);
                }
            }
        }

        return dp[0].back();
    }
};
```

