### 三、503.下一个更大元素II

> 给定一个循环数组 `nums` （ `nums[nums.length - 1]` 的下一个元素是 `nums[0]` ），返回 *`nums` 中每个元素的 **下一个更大元素*** 。
>
> 数字 `x` 的 **下一个更大的元素** 是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 `-1` 。
>
> **示例 1:**
>
> ```
> 输入: nums = [1,2,1]
> 输出: [2,-1,2]
> 解释: 第一个 1 的下一个更大的数是 2；
> 数字 2 找不到下一个更大的数； 
> 第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
> ```
>
> **示例 2:**
>
> ```
> 输入: nums = [1,2,3,4,3]
> 输出: [2,3,4,-1,4]
> ```

[503. 下一个更大元素 II - 力扣（LeetCode）](https://leetcode.cn/problems/next-greater-element-ii/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0503.下一个更大元素II.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    void printVec(vector<int> &vec){
        for(int i:vec){
            cout<<i<<'\t';
        }
        cout<<endl;
    }
    vector<int> nextGreaterElements(vector<int>& nums) {
        // 遍历两次数组即可（相当于将两段nums拼接）
        stack<int> st;
        vector<int> result(nums.size(), -1);

        for (int i = 0; i < (2 * nums.size()); i++) {
            // cout<<"i:"<<i<<endl;
            int index = i % nums.size();
            // cout<<"index:"<<index<<endl;
            // cout<<"num:"<<nums[index]<<endl;
            while (!st.empty() && nums[index] > nums[st.top()]){
                result[st.top()]=nums[index];
                st.pop();
            }
            st.push(index);
            // printVec(result);
            
        }

       return result; 
    }
};
```

### 四、42. 接雨水

> 给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
>
> **示例 1：**
>
> ![img](./day49_单调栈Part2.assets/rainwatertrap.png)
>
> ```
> 输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
> 输出：6
> 解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
> ```
>
> **示例 2：**
>
> ```
> 输入：height = [4,2,0,3,2,5]
> 输出：9
> ```

[42. 接雨水 - 力扣（LeetCode）](https://leetcode.cn/problems/trapping-rain-water/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0042.接雨水.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        // 找到右边第一个更高的柱子，找到左边第一个更高的柱子
        // 当前凹槽所装雨水的高度是两侧更高柱子的较小值
        // 所装雨水的宽度是两根柱子之间的距离
        int result = 0;
        stack<int> st;

        int leftHigher_index = 0;
        int rightHigher_index = 0;
        int middle_index = 0;
        int notchWeight = 0; // 凹槽宽度
        int notchDepth = 0;  // 凹槽深度
        int water = 0;

        // 要先将height[0]压栈，否则遇到凹槽在第0个位置时会出现栈访问异常
        st.push(0);
        for (int i = 1; i < height.size(); i++) {
            while (!st.empty() && height[i] > height[st.top()]) {
                rightHigher_index = i;
                middle_index = st.top();

                st.pop();
                if (!st.empty()) {
                    leftHigher_index = st.top();
                    // 注意要计算的凹槽是即将从栈中弹出的柱子
                    notchWeight = // 计算凹槽宽度
                        rightHigher_index - leftHigher_index - 1;
                    notchDepth = // 计算凹槽深度
                        min(height[rightHigher_index],
                            height[leftHigher_index]) -
                        height[middle_index];

                    water = notchWeight * notchDepth;
                    result += water;
                }
            }
            st.push(i);
        }

        return result;
    }
};
```

### 五、84.柱状图中最大的矩形

> 给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
>
> 求在该柱状图中，能够勾勒出来的矩形的最大面积。
>
> **示例 1:**
>
> <img src="./day49_单调栈Part2.assets/histogram.jpg" alt="img" style="zoom: 67%;" />
>
> ```
> 输入：heights = [2,1,5,6,2,3]
> 输出：10
> 解释：最大的矩形为图中红色区域，面积为 10
> ```
>
> **示例 2：**
>
> <img src="./day49_单调栈Part2.assets/histogram-1.jpg" alt="img" style="zoom:67%;" />
>
> ```
> 输入： heights = [2,4]
> 输出： 4
> ```

[84. 柱状图中最大的矩形 - 力扣（LeetCode）](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0084.柱状图中最大的矩形.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int result = 0;
        stack<int> st;

        // 在首尾添加高度为0的矩形
        heights.insert(heights.begin(), 0);
        heights.push_back(0);

        int leftLower_index = 0;
        int rightLower_index = 0;
        int middle_index = 0;
        int height = 0;
        int width = 0;
        int area = 0;

        for (int i = 0; i < heights.size(); i++) {
            while (!st.empty() && heights[i] < heights[st.top()]) {
                rightLower_index = i;
                middle_index = st.top();
                st.pop();
                if (!st.empty()) {
                    leftLower_index = st.top();
                    height = heights[middle_index];
                    width = rightLower_index - leftLower_index - 1;
                    area = height * width;
                    result = max(area,result);
                }
            }
            st.push(i);
        }

        return result;
    }
};
```