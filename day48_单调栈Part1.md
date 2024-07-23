# chap 12 单调栈

### 一、739. 每日温度

> 给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。
>
> **示例 1:**
>
> ```
> 输入: temperatures = [73,74,75,71,69,72,76,73]
> 输出: [1,1,4,2,1,1,0,0]
> ```
>
> **示例 2:**
>
> ```
> 输入: temperatures = [30,40,50,60]
> 输出: [1,1,1,0]
> ```
>
> **示例 3:**
>
> ```
> 输入: temperatures = [30,60,90]
> 输出: [1,1,0]
> ```

[739. 每日温度 - 力扣（LeetCode）](https://leetcode.cn/problems/daily-temperatures/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0739.每日温度.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        // 声明并初始化递增单调栈
        stack<int> st;
        // 存放结果
        vector<int> result(temperatures.size(), 0);

        for (int i = 0; i < temperatures.size(); i++) {
            while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
                result[st.top()] = i - st.top();
                st.pop();
            }
            st.push(i);
        }

        return result;
    }
};
```

### 二、496.下一个更大元素 I

> 给你两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。
>
> 请你找出 nums1 中每个元素在 nums2 中的下一个比其大的值。
>
> nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。
>
> 示例 1:
>
> 输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
> 输出: [-1,3,-1]
> 解释:
> 对于 num1 中的数字 4 ，你无法在第二个数组中找到下一个更大的数字，因此输出 -1 。
> 对于 num1 中的数字 1 ，第二个数组中数字1右边的下一个较大数字是 3 。
> 对于 num1 中的数字 2 ，第二个数组中没有下一个更大的数字，因此输出 -1 。
>
> 示例 2:
> 输入: nums1 = [2,4], nums2 = [1,2,3,4].
> 输出: [3,-1]
> 解释:
> 对于 num1 中的数字 2 ，第二个数组中的下一个较大数字是 3 。
> 对于 num1 中的数字 4 ，第二个数组中没有下一个更大的数字，因此输出-1 。

[496. 下一个更大元素 I - 力扣（LeetCode）](https://leetcode.cn/problems/next-greater-element-i/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0496.下一个更大元素I.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        // 结果数组初始化为-1
        vector<int> result(nums1.size(), -1);

        // 建立nums2到nums1的kv映射，其中k=nums1[i],v=i;
        unordered_map<int, int> umap;
        for (int i = 0; i < nums1.size(); i++) {
            umap[nums1[i]] = i;
        }

        stack<int> st;
        // 遍历nums2
        for (int i = 0; i < nums2.size(); i++) {
            while (!st.empty() && nums2[i] > st.top()) {
                if (umap.find(st.top()) != umap.end()) {
                    result[umap[st.top()]]= nums2[i];
                }
                st.pop();
            }
            st.push(nums2[i]);
        }

        return result;
    }
};
```

