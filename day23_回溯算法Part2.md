### 五、39. 组合总和

[39. 组合总和 - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0039.组合总和.html#算法公开课)

#### 1、思路

- 组合可以存在重复元素，因此每次递归从i开始即可；

#### 2、注意点

- 终止条件必须要有`if(sum>target)`，否则可能无限递归导致栈溢出；

#### 3、代码

```c++
 class Solution {
 private:
     vector<vector<int>> result;
     vector<int> path;
     void backtracking(vector<int> candidates, int target, int sum,
                       int start_index) {
         // 如果当前和已经超过目标，则返回
         if(sum>target)
         {
             return;
         }
         // 当前和等于目标，加入result
         if (sum == target) {
             result.push_back(path);
             return;
         }
 
         for (int i = start_index; i < candidates.size(); i++) {
             
             sum+=candidates[i];
             path.push_back(candidates[i]);
 
             backtracking(candidates,target,sum,i);
 
             path.pop_back();
             sum-=candidates[i];
         }
     }
 
 public:
     vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
         backtracking(candidates,target,0,0);
         return result;
     }
 };
```

### 六、40.组合总和II

[代码随想录 (programmercarl.com)](https://programmercarl.com/0040.组合总和II.html)

[40. 组合总和 II - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum-ii/description/)

#### 1、思路

1. 数组中有重复数组，但最后的组合不能有相同组合，如[1,7]和[7,1]；
2. 因此将数组先**排序**，再用一个**used数组记录已经使用的数据**；
3. 如果前一个相同的数未被使用，说明已经**从前一个数的循环中回溯了**，当前数再找组合会找到相同组合，因此直接跳过；

#### 2、代码

```c++
 class Solution {
 private:
     vector<vector<int>> result;
     vector<int> path;
     void backtracking(vector<int> sorted_candidates, int target, int sum,
                       int start_index, vector<int> used) {
         if (sum > target) {
             return;
         }
         if (sum == target) {
             result.push_back(path);
         }
 
         for (int i = start_index; i < sorted_candidates.size(); i++) {
             // 在排序后的数组中，如果当前数和前一个数相同
             // 且已经从前一个数的回溯中跳出（used[i-1]==0）
             // 则说明该数对应的组合已经被前一个数遍历完了，不遍历当前数
             if (i > 0 && sorted_candidates[i] == sorted_candidates[i - 1] &&
                 used[i - 1] == 0) {
                 continue;
             }
 
             sum += sorted_candidates[i];
             path.push_back(sorted_candidates[i]);
             used[i] = 1;
 
             backtracking(sorted_candidates, target, sum, i + 1, used);
 
             used[i] = 0;
             path.pop_back();
             sum -= sorted_candidates[i];
         }
     }
 
 public:
     vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
         vector<int> used(candidates.size());
         sort(candidates.begin(),candidates.end()); // 对数组先排序
         backtracking(candidates, target, 0, 0,used);
         return result;
     }
 };
```

### 七、131.分割回文串

[131. 分割回文串 - 力扣（LeetCode）](https://leetcode.cn/problems/palindrome-partitioning/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0131.分割回文串.html#思路)

#### 1、思路

1. 题目是要找到一种分割方法，将一个字符串分割成若干回文子串；事实上和组合问题本质是相同的，区别在于要找的是**连续的组合**；
2. 分割子串[start,i]，如果子串是回文串，则收集子串，**递归调用：将start移到i+1**；
3. 如果子串不是回文串，则**直接i++**，查看**[start,i+1]**是不是回文串；
4. **终止条件**：当**start递归到s.size()时**，说明整个字符已经被成功分割成多个回文串，保存在path中，将path压入result中； 

<img src="./day23_回溯算法Part2.assets/image-20240630234335554.png" alt="image-20240630234335554" style="zoom: 25%;" />

#### 2、代码

```c++
 class Solution {
 private:
     vector<string> path; // 存储每一种子串分割
     vector<vector<string>> result; // 存储最终结果
 
     // 判断一个字符串的某段子串是不是回文串
     bool isParlindrome(const string& s, int start, int end) {
         for (int i = start, j = end; i < j; i++, j--) {
             if (s[i] != s[j]) {
                 return false;
             }
         }
         return true;
     }
 
     void backtracking(const string& s, int start_index) {
         if (start_index == s.size()) {
             result.push_back(path);
             return;
         }
 
         for (int i = start_index; i < s.size(); i++) {
             // 如果当前选取的子串[start_index,i]是回文串
             if (isParlindrome(s, start_index, i)) {
                 // 获取子串，将其加入path
                 string pStr = s.substr(start_index, i - start_index + 1);
                 path.push_back(pStr);
             } 
             // 否则直接将i后移，增长子串的长度
             else {
                 continue;
             }
 
             // 递归调用
             backtracking(s, i + 1);
 
             // 回溯
             path.pop_back();
         }
         return;
     }
 
 public:
     vector<vector<string>> partition(string s) {
         backtracking(s,0);
         return result;
     }
 };
```
