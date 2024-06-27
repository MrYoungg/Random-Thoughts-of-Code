### 一、回溯算法理论基础

[代码随想录 (programmercarl.com)](https://programmercarl.com/回溯算法理论基础.html#理论基础)
<img src="./day22_回溯算法Part1.assets/20210219192050666.png" alt="回溯算法大纲" style="zoom: 33%;" />

#### 1、回溯理解

1. 回溯是蕴含在递归中的思想，在[112. 路径总和 - 力扣（LeetCode）](https://leetcode.cn/problems/path-sum/description/)中有了初步体会，事实上回溯就是**完成了一种可能后，返回上一状态去找下一种可能**的过程；
2. 回溯的过程可以用n叉树非常直观地表示出来，以组合问题为例：在[1,4]中找长度为2的所有组合，用树表示如下；树的宽度代表需要for循环遍历的次数，树的深度代表完成选择需要递归的深度；
    <img src="./day22_回溯算法Part1.assets/20201123195223940.png" alt="77.组合" style="zoom:50%;" />

#### 2、回溯模板

```c++
void backtracking(参数) {
    if (终止条件，通常是找到结果) {
        存放结果;
        return;
    }

    for (遍历当前递归层的节点) {
        处理当前节点;
        backtracking(); // 递归深入下一层
        回溯，撤销递归中的结果，寻找下一种可能
    }
}
```

### 二、77.组合

[77. 组合 - 力扣（LeetCode）](https://leetcode.cn/problems/combinations/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0077.组合.html)

#### 1、思路

<img src="./day22_回溯算法Part1.assets/20201123195223940-1719499508471-5.png" alt="77.组合" style="zoom:50%;" />

1. 终止条件：当前数组长度满足长度要求k；
2. for循环：横向遍历每一个数；
3. 递归：纵向遍历每种可能的组合；
4. 剪枝：当剩余的数不可能满足k的要求时，无需继续遍历；

#### 2、代码

##### （1）完全遍历

```c++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> sub_vec;
    void backtracking(int n, int k, int start_index) {
        // 终止条件，一组的数量达到k
        if (sub_vec.size() == k) {

            // printSubVec(sub_vec); // 打印日志
            result.push_back(sub_vec);
            return;

        }

        // 逐节点遍历
        for (int i = start_index; i < n + 1; i++) {

            sub_vec.push_back(i);
            // printSubVec(sub_vec); // 打印日志

            backtracking(n, k, i + 1); // 深入到最终符合条件的叶子节点

            sub_vec.pop_back();        // 回溯
            // printSubVec(sub_vec); // 打印日志

        }

        return;
    }

    void printSubVec(const vector<int> sub_vec) {
        cout << "result：" ;
        for (auto i : sub_vec) {
            cout<< i << " ";
        }
        cout << endl;
    }

public:
    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);
        return result;
    }
};
```

##### （2）剪枝

```c++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> sub_vec;
    void backtracking(int n, int k, int start_index) {
        // 终止条件
        if (sub_vec.size() == k) {

            // printSubVec(sub_vec); // 打印日志
            result.push_back(sub_vec);
            return;

        }

        // 逐节点遍历
        // 剪枝：当剩余数字已无法满足长度要求时，无需遍历
        for (int i = start_index; i <= (n+1)-(k-sub_vec.size()); i++) {
            
            // 剪枝，事实上就是i > (n+1)-(k-sub_vec.size)时,不执行循环
            // 可以将上方for循环条件改为：i <= (n+1)-(k-sub_vec.size())
            // if((n+1)-i < k-sub_vec.size()) 
            // {
            //     break;
            // }

            sub_vec.push_back(i);
            // printSubVec(sub_vec); // 打印日志

            backtracking(n, k, i + 1); // 递归深入到最终符合条件的叶子节点

            sub_vec.pop_back();        // 回溯
            // printSubVec(sub_vec); // 打印日志

        }

        return;
    }

    void printSubVec(const vector<int> sub_vec) {

        cout << "result：" ;
        for (auto i : sub_vec) {
            cout<< i << " ";
        }
        cout << endl;

    }

public:
    vector<vector<int>> combine(int n, int k) {
        
        backtracking(n, k, 1);
        return result;

    }
};
```

### 三、216.组合总和III

[216. 组合总和 III - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum-iii/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0216.组合总和III.html)

#### 1、思路

![216.组合总和III](./day22_回溯算法Part1.assets/20201123195717975-20230310113546003.png)

1. 参数：数组长度要求count，目标和target_sum，当前和sum，当前层的数字起始位置start_index；
2. 终止条件：数组达到长度要求，数组和达到目标和要求；

#### 2、注意点

1. 有两次剪枝操作：
    1. 如果当前数字的和已经超过目标和，无需继续遍历；
    2. 如果剩下的数字无法满足个数的要求，无需继续遍历；
2. 递归之前变化的值在递归之后都要回溯，在这里包括sum和result；

#### 3、代码

```c++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    void backtracking(int count, int target_sum, int sum, int start_index) {
        // 剪枝1:尽管还没递归到足够的数字，但sum已经超过target_sum，无需继续
        if (sum > target_sum) {
            return;
        }

        // 找到足够的数字
        if (path.size() == count) {
            if (sum == target_sum) {
                result.push_back(path);
            }
            return;
        }

        // 横向遍历
        // 剪枝2:剩余的数不足以满足count的要求时,无需继续
        for (int i = start_index; i <= 9-(count-path.size())+1; i++) 
        {
            sum += i;
            path.push_back(i);
            backtracking(count, target_sum, sum, i + 1); // 纵向递归
            path.pop_back();                             // 回溯
            sum -= i;                                    // 回溯
        }
        return;
    }

public:
    vector<vector<int>> combinationSum3(int k, int n) {
        backtracking(k,n,0,1);
        return result;
    }
};
```

### 四、17.电话号码的字母组合

[17. 电话号码的字母组合 - 力扣（LeetCode）](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0017.电话号码的字母组合.html)

#### 1、思路

1. 参数：数字组成的字符串；
2. 终止条件：字母字符串长度等于数字字符串长度（每个数字都有一组对应字母，因此有几个数字，字母字符串就有多长）；
3. 递归调用和回溯：for循环要完全遍历每个数字对应的**所有字母**；digits_index来依次遍历**数字字符串**中的所有数字，完成递归；

#### 2、注意点

1. 数字到字母的映射实现；

    ```c++
    // 数字到字母的映射表
        string NumToLetter_Map[10] = {
            "",     // 0,填充对齐
            "",     // 1,填充对齐
            "abc",  // 2
            "def",  // 3
            "ghi",  // 4
            "jkl",  // 5
            "mno",  // 6
            "pqrs", // 7
            "tuv",  // 82
            "wxyz"  // 9
        };
    ```

2. 注意考虑传入字符串为空的情况；

#### 3、代码

```c++
class Solution {
private:
    // 数字到字母的映射表
    string NumToLetter_Map[10] = {
        "",     // 0,填充对齐
        "",     // 1,填充对齐
        "abc",  // 2
        "def",  // 3
        "ghi",  // 4
        "jkl",  // 5
        "mno",  // 6
        "pqrs", // 7
        "tuv",  // 82
        "wxyz"  // 9
    };
    vector<string> result;
    string path;
    int digits_index; // 传入的数字字符串的索引

public:
    vector<string> letterCombinations(string digits) {

        // 注意考虑digits为空的情况
        if (digits.size() == 0) 
        {
            return result;
        }

        // 该条件无法处理digits为空的情况,result中会压入一个空字符串
        if (path.size() == digits.size()) {
            result.push_back(path);
            return result;
        }

        int digit_int = digits[digits_index] - '0'; // 从字符char转换为数字int
        string letters = NumToLetter_Map[digit_int]; // 获取对应的字母映射

        for (int i = 0; i < letters.size(); i++) // 遍历当前数字对应的所有字符
        {
            path.push_back(letters[i]);
            digits_index++;
            letterCombinations(digits); // 纵向递归下一个数字
            digits_index--;             // 回溯
            path.pop_back();            // 回溯
        }

        return result;
    }
};
```

