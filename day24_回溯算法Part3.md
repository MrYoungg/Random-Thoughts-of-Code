### 八、93.复原IP地址

[93. 复原 IP 地址 - 力扣（LeetCode）](https://leetcode.cn/problems/restore-ip-addresses/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0093.复原IP地址.html#算法公开课)

#### 1、思路

1. 该题是分割问题，分割在本质上也是组合问题，区别在于要组合的是连续元素；
2. **终止条件：**已经放置三个分割点“.“，则可以终止；若最后一节也是合法IP，则将整体加入result，否则返回，回溯上一层；
    ![93.复原IP地址](./day24_回溯算法Part3.assets/20201123203735933.png)

#### 2、注意点

1. IP合法性判断
    - **start超过end**之后不合法：避免出现结尾是空字符的情况，如**”101023“**得到**“10.10.23.”**；
    - 开头字符为0的非零数值不合法；
    - 字符不属于’0’~‘9’不合法；
    - 子串整体数值超过255不合法
2. 在当前循环中**遇到不合法IP**即可结束整个循环，无需i++进行下次循环（使用break而不是continue）；因为i继续向后遍历也**必然是不合法的IP**

#### 3、代码

```c++
class Solution {
private:
    vector<string> result;
    // 判断字符串s中的子字符串[start,end]是不是合法IP
    bool isValid(const string& s, int start, int end) {

        // start超过end之后不合法：避免出现结尾是空字符的情况,如101023得到10.10.23.
        if(start>end)
        {
            return false;
        }
        
        // 以0开头的非零数字不合法
        if (s[start] == '0' && start != end) {
            return false;
        }

        int num = 0;
        for (int i = start; i <= end; i++) {

            // 包含其他符号不合法
            if (s[i] > '9' || s[i] < '0') {
                return false;
            }

            num = num * 10 + (int)(s[i] - '0');
            // 数字大于255不合法
            if (num > 255) {
                return false;
            }
        }

        return true;
    }

    void backtracking(string &s, int start_index, int point_num) {
        // 已经添加了3个分割点，判断最后一段字符是不是合法IP
        // 如果合法则加入result中
        if (point_num == 3) {
            if (isValid(s, start_index, s.size() - 1)) {
                cout << "push to result:" << s << endl << endl;
                result.push_back(s);
            }
            return;
        }

        for (int i = start_index; i < s.size(); i++) {
            cout << "start_index:" << start_index << endl;
            cout << "i:" << i << endl;
            // 如果[start_index,i]是合法IP，则添加分割点
            if (isValid(s, start_index, i)) {
                s.insert(s.begin() + i + 1, '.');
                point_num++;
                cout << "s:" << s << endl;
                cout << "point_num:" << point_num << endl << endl;
            } else {
                break;
            }

            backtracking(s, i + 2, point_num); // 递归

            point_num--;                // 回溯
            s.erase(s.begin() + i + 1); // 回溯
        }
    }

public:
    vector<string> restoreIpAddresses(string s) {
        backtracking(s, 0, 0);
        return result;
    }
};
```

### 九、78.子集

[78. 子集 - 力扣（LeetCode）](https://leetcode.cn/problems/subsets/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0078.子集.html#算法公开课)

#### 1、思路

1. 子集问题是广义的组合问题，前面的组合题目事实上是**加了限制条件的子集问题**，如**限制子集的元素数量**，**限制子集的元素和**等；
2. 因此子集与组合的区别在于，不需要等到**限制条件满足（回溯树的叶子节点）**才收集结果，在**每个节点**都应该收集一次结果；

#### 2、注意点

- 注意空集是所有集合的子集，记得将空集加入结果数组中；

#### 3、代码

```c++
class Solution {
private:
    vector<int> path;
    vector<vector<int>> result;

    void backtracking(vector<int>& nums, int start_index) {
        if (start_index == nums.size()) {
            return;
        }

        for (int i = start_index; i < nums.size(); i++) {

            path.push_back(nums[i]);
            result.push_back(path); // 获取每一个树节点上的path

            backtracking(nums, i + 1); // 递归

            path.pop_back(); // 回溯
        }
    }

public:
    vector<vector<int>> subsets(vector<int>& nums) {
        result.push_back(vector<int>()); // 将空集插入结果中
        backtracking(nums, 0);           // 获取非空子集
        return result;
    }
};
```

### 十、90.子集II

[90. 子集 II - 力扣（LeetCode）](https://leetcode.cn/problems/subsets-ii/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0090.子集II.html#算法公开课)

#### 1、思路

1. 本题是子集+去重，类似于[40.组合总和II](#jump)的组合+去重；
2. 关键在于**用used数组区分是树层还是树枝**；如果是在树层遇到相同的数，则跳过，如果是在树枝遇到则是一个合法子集；

#### 2、注意点

- 注意在调用backtracking前要对数组进行**排序**；

#### 3、代码

```c++
class Solution {
private:
    vector<int> path;
    vector<vector<int>> result;

    // 传入排序后的数组，获取其非空集
    void backtracking(vector<int>& nums, int start_index, vector<int>& used) {
        if (start_index == nums.size()) {
            return;
        }

        for (int i = start_index; i < nums.size(); i++) {
            // 从回溯中出来以后，后续相同的数无需进入递归判断，以去除相同的子集
            if (i > 0 && nums[i] == nums[i - 1] && used[i - 1] == 0) {
                continue;
            }

            path.push_back(nums[i]);
            used[i] = 1;
            result.push_back(path); // 收集每一个节点的path

            backtracking(nums, i + 1, used);

            used[i] = 0;
            path.pop_back();
        }
    }

public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        
        // 可以初始化在编译时不确定大小的vector
        // 但不能初始化这样的数组：int used[nums.size()]={0};
        vector<int> used(nums.size(), 0);

        result.push_back(vector<int>()); // 向结果中加入空集
        sort(nums.begin(), nums.end()); // 将nums排序

        backtracking(nums, 0, used);
        return result;
    }
};
```
