### 一、哈希表基础知识

[代码随想录 (programmercarl.com)](https://programmercarl.com/哈希表理论基础.html#常见的三种哈希结构)

### 二、有效字母的异位词

[242. 有效的字母异位词 - 力扣（LeetCode）](https://leetcode.cn/problems/valid-anagram/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0242.有效的字母异位词.html#算法公开课)

[学透哈希表，数组使用有技巧！Leetcode：242.有效的字母异位词_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1YG411p7BA/)

#### 1、思想

1. 定义一个数组（size=26，数组本质上就是一个哈希表，哈希函数为y=x），记录字符串中所有字母出现的次数；
2. 遍历第二个字符串，将出现的字母依次在数组中-1,；
3. 遍历数组，如果数组全为0，代表找到了字母异位词，否则说明不是异位词；

#### 2、注意点

1. 访问数组元素时，下标为str[]-‘a’；

#### 3、代码

```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        int record[26]={0};
        for(int i=0;i<s.size();i++)
        {
            record[s[i]-'a']++;
        }
        for(int i=0;i<t.size();i++)
        {
            record[t[i]-'a']--;
        }
        for(int i =0;i<26;i++)
        {
            if(record[i]!=0)
            {
                return false;
            }
        }
        return true;
    }
};
```

### 二、两个数组的交集

[349. 两个数组的交集 - 力扣（LeetCode）](https://leetcode.cn/problems/intersection-of-two-arrays/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0349.两个数组的交集.html#思路)

[学透哈希表，set使用有技巧！Leetcode：349. 两个数组的交集_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ba411S7wu/?vd_source=1e61d896f0ce15962eab39c0507f505d)

#### 1、思路

1. 将nums1放入哈希表实现的`unordered_set`中；
    - **set**：有序+不允许重复
    - **unordered_set**：无序+不允许重复
    - **unordered_multiset**：无序+允许重复
2. 遍历nums2，通过`nums1_set.find(num)`寻找nums1中是否存在当前遍历到的元素；
3. 如果存在，将其加入result（即最后输出的子集），result也用`unordered_set`结构，目的是为了去重（unordered_set结构在insert()的时候，不会重复插入）;

#### 2、注意点

1.  `for(int num:nums2)`在C++11中可以用来遍历容器；
2.  可以用begin和end迭代器初始化容器：`unordered_set<int> nums1_set(nums1.begin(),nums1.end());`和`vector<int>(result.begin(),result.end());`

#### 3、代码

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> result;
        unordered_set<int> nums1_set(nums1.begin(),nums1.end());

        for(int num:nums2)
        {
            if(nums1_set.find(num)!=nums1_set.end())
            {
                result.insert(num);
            }
        }

        return vector<int>(result.begin(),result.end());

    }
};
```

### 三、快乐数

[202. 快乐数 - 力扣（LeetCode）](https://leetcode.cn/problems/happy-number/submissions/538604819/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0202.快乐数.html#思路)

#### 1、思想

1. 将每次求出来的”各位的平方和“放入一个`unordered_set`中，如果发现已存在，说明发生了循环——不是快乐数；
2. 如果“各位的平方和”出现1，则代表是快乐数；

#### 2、注意点

1. `getSum(int n)`函数的实现；

2. 如何判断record_set中是否已经存在传入的元素

    1. insert()方法会返回一个pair对象，其有两个元素，pair.first：元素插入的位置或已存在的位置；pair.second：true=插入成功，false=元素已存在，插入失败；

    ```c++
    //pair的通用形式
    template <class T1, class T2>
    struct pair {
        T1 first;
        T2 second;
    }
    ```

    1. 也可以通过find()方法实现

    ```c++
    if(record_set.find(num)!=record_set.end())
    {
    	return false;
    }
    else
    {
        record_set.insert(num);
    }
    ```

#### 3、代码

```c++
#include <iostream>
using namespace std;
#include <vector>
#include <unordered_set>

class Solution
{
public:
    int getSum(int n)
    {
        int sum = 0;
        while (n != 0)
        {
            sum += (n % 10) * (n % 10);
            n /= 10;
        }
        return sum;
    }
    bool isHappy(int n)
    {
        unordered_set<int> record_set;
        int num = n;
        while (1)
        {
            num = getSum(num);
            if (num == 1)
            {
                return true;
            }

            // 用flag来承接insert()返回的pair对象；
            // pair.first：元素插入的位置或已存在的位置；
            // pair.second：true=插入成功，false=元素已存在，插入失败；
            auto flag = record_set.insert(num);

            if (flag.second == false)
            {
                return false;
            }
        }
    }
};
```

### 四、两数之和

[1. 两数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/two-sum/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0001.两数之和.html#思路)

[梦开始的地方，Leetcode：1.两数之和，学透哈希表，map使用有技巧！_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1aT41177mK/)

#### 1、思想

1. 构造一个`unordered_map`，遍历一次数组，在`unordered_map`中寻找是否存在`（target-nums[i]）`；
2. 如果不存在，将当前遍历的元素数据->元素索引以key->value的形式存入`unordered_map`中；
3. 如果存在，证明找到符合要求的两个数，返回i和map中元素的value；

#### 2、注意点

1. record_map.find()方法返回值为：
    1. **元素存在**：指向该元素的迭代器；
    2. **元素不存在**：返回end迭代器；
    3. 值得注意的是，其迭代器的内部实现是**pair对象**，first=key，second=value；
2. 注意遍历完数组后仍未找到，代表无符合的两数，返回空数组；

#### 3、代码

```c++
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

class Solution
{
public:
    vector<int> twoSum(vector<int> &nums, int target)
    {
        unordered_map<int, int> record_map;

        for (int i = 0; i < nums.size(); i++)
        {
            int diff = target - nums[i];
            auto map_flag = record_map.find(diff);

            if (map_flag != record_map.end())
            {
                return {map_flag->second, i};
            }
            else
            {
                record_map.insert(make_pair(nums[i], i));
            }
        }
        return {NULL};
    }
};
```

