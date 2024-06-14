### 一、454.四数相加

[454. 四数相加 II - 力扣（LeetCode）](https://leetcode.cn/problems/4sum-ii/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0454.四数相加II.html#算法公开课)

#### 1、思路

1. 将四数之和看成两数之和，nums1+nums2为一数（遍历两个数组，O(n^2)），nums3+nums4为一数；
2. 第一次遍历时，在`unordered_map`中存入：key=nums1[i]+nums2[j]，value=该key值出现的次数；
3. 第二次遍历时，在`unordered_map`中寻找：key=-nums3(k)-nums4[l]（代表四数相加为0），如果找到，result_count+=value（存在的四元组数量增加）；

#### 2、注意点

1. 访问key对应value的方法：record_map[key]，就代表了key对应的value；

#### 3、代码

```c++
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
class Solution
{
public:
    int fourSumCount(vector<int> &nums1, vector<int> &nums2, vector<int> &nums3, vector<int> &nums4)
    {
        unordered_map<int, int> record_map;
        int result_count = 0; // 局部变量记得初始化,否则输出随机值

        for (int A : nums1)
        {
            for (int B : nums2)
            {
                record_map[A + B]++;
            }
        }

        for (int C : nums3)
        {
            for (int D : nums4)
            {
                auto flag = record_map.find(-C - D);
                if (flag != record_map.end())
                {
                    result_count += flag->second;
                }
            }
        }
        return result_count;
    }
};
```

### 二、383.赎金信

#### 1、思路

1. 类似于**242.有效字母的异位词**，不同之处在于该题是判断ransomNote是不是magazine的子集；
2. 由于只有小写字母，可以用长度为26的数组存储每一个字母在magazine出现的次数；
3. 遍历ransomNote，将每个字母的出现次数从数组中--，如果有某个元素减为0代表失败；否则成功；

#### 2、注意点

​	如果数据大小固定不变，能用数组就用数组，用map时需要维护哈希表或者红黑树，还需要执行哈希函数，开销较大；

#### 3、代码

1. **自敲**

    ```c++
    class Solution {
    public:
        bool canConstruct(string ransomNote, string magazine) 
        {
            unordered_map<char,int> record_map;
            int i=0;
            while(magazine[i]!='\0')
            {
                record_map[magazine[i]]++;
                i++;
            }
    
            int j=0;
            while(ransomNote[j]!='\0')
            {
                auto flag=record_map.find(ransomNote[j]);
                if(flag!=record_map.end()&&record_map[ransomNote[j]]!=0)
                {
    
                    // cout<<ransomNote[j]<<":"<<record_map[ransomNote[j]]<<endl;
                    record_map[ransomNote[j]]--;
                }
                else
                {
                    return false;
                }
                j++;
            }
    
            return true;
        }
    };
    ```

2. **优化**

    ```c++
    class Solution {
    public:
        bool canConstruct(string ransomNote, string magazine) 
        {
            int record_arr[26]={0};
    
            for(int i=0;magazine[i]!='\0';i++)
            {
                record_arr[magazine[i]-'a']++;
            }
    
            for(int i=0;ransomNote[i]!='\0';i++)
            {
                if(record_arr[ransomNote[i]-'a']!=0)
                {
                    record_arr[ransomNote[i]-'a']--;
                }
                else
                {
                    return false;
                }
            }
    
            return true;
        }
    };
    ```

### 三、15.三数之和

[15. 三数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/3sum/submissions/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0015.三数之和.html#思路)

[梦破碎的地方！| LeetCode：15.三数之和_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1GW4y127qo/?vd_source=1e61d896f0ce15962eab39c0507f505d)

#### 1、思路

1. 哈希法比较复杂，采用双指针法，注意题目要求的不能有重复的三元组；
2. 先将数组用sort()排序，此时所有重复的数据都会聚集在一起，方便后续查找和去重；
3. 用i遍历整个数组，left指向i+1，right指向数组末尾元素；若三数之和大于0，则right--来减小和；若三数之和小于0，则left++来增大；三数之和等于0是，push_back()进入结果数组result；
4. 找到一组三元组后，left和right相向而行，并跳过路程中的相等元素，继续寻找下一组三元组，直至left==right，跳出循环，i遍历下一个元素（i也需要跳过相等元素）；

#### 2、注意点

1. 去重的逻辑要注意，在有序数组中，相等的元素总是紧靠在一起，因此一起跳过即可；
2. 注意在i遍历到大于0的元素时，此后不可能再找到符合条件的三元组，因此直接跳出循环，返回结果；

#### 3、代码

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Solution
{
public:
    vector<vector<int>> threeSum(vector<int> &nums)
    {
        vector<vector<int>> result;
        // 先对数组进行排序，对后续操作很重要！
        sort(nums.begin(), nums.end());

        for (int i = 0; i < nums.size(); i++)
        {
            // 如果当前遍历到的元素大于0，则后续没有符合条件的三元组，直接返回
            if (nums[i] > 0)
            {
                // cout << "nums[0]>0" << endl;
                break;
            }

            // 先对nums[i]进行去重,注意数组是有序的；
            if (i > 0 && nums[i] == nums[i - 1])
            {
                // cout << "nums[i]去重" << endl;
                continue;
            }

            // 定义双指针
            int left = i + 1;
            int right = nums.size() - 1;
            int loops = 0;
            while (left < right)
            {
                // cout << "loops:" << loops;
                loops++;
                // 此时的三元组和太大，right应该回缩；
                if (nums[i] + nums[left] + nums[right] > 0)
                {
                    // cout << "right:" << right << " " << "right--" << endl;
                    right--;
                }
                // 此时的三元组和太小，left应该前移；
                else if (nums[i] + nums[left] + nums[right] < 0)
                {
                    // cout << "left:" << left << " " << "left++" << endl;
                    left++;
                }
                // 相等时，将三元组纳入result
                else
                {
                    // cout << "找到三元组" << endl;
                    // cout << nums[i] << " " << nums[left] << " " << nums[right] << endl;
                    result.push_back(vector<int>{nums[i], nums[left], nums[right]});
                    left++;
                    right--;
                    // 找到一个三元组后，对nums[j]和nums[k]去重,注意数组是有序的
                    while (left < right && nums[left] == nums[left - 1])
                    {
                        // cout << "nums[j]去重" << endl;
                        left++;
                    }
                    while (left < right && nums[right] == nums[right + 1])
                    {
                        // cout << "nums[k]去重" << endl;
                        right--;
                    }
                }
            }
        }
        return result;
    }
};
```

### 四、18.四数之和

[18. 四数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/4sum/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0018.四数之和.html#算法公开课)

#### 1、思路

1. 类似于上一题**15.三数之和**的思路，只不过需要两层for循环来遍历得到a+b；
2. 再通过双指针得到c+d；

#### 2、注意点

1. 两层剪枝的操作与**15.三数之和**有区别，因为三数之和的target确定是0，而这题的target可以小于0，因此情况不同；

#### 3、代码

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Solution
{
public:
    vector<vector<int>> fourSum(vector<int> &nums, int target)
    {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());

        // 第一层循环,遍历找a
        for (int a = 0; a < nums.size(); a++)
        {
            // 剪枝，去掉一部分情况
            if (nums[a] >= 0 && nums[a] > target)
            {
                break;
            }

            // 为a去重，跳过相同的元素
            if (a > 0 && nums[a] == nums[a - 1])
            {
                continue;
            }

            // 第二层循环，遍历寻找b
            for (int b = a + 1; b < nums.size(); b++)
            {
                // 第二层剪枝
                if (nums[a] + nums[b] >= 0 && nums[a] + nums[b] > target)
                {
                    break;
                }

                // 为b去重
                if (b > a + 1 && nums[b] == nums[b - 1])
                {
                    continue;
                }

                // 定义双指针
                int left = b + 1;
                int right = nums.size() - 1;
                while (left < right)
                {
                    if ((long)nums[a] + nums[b] + nums[left] + nums[right] > target)
                    {
                        right--;
                    }
                    else if ((long)nums[a] + nums[b] + nums[left] + nums[right] < target)
                    {
                        left++;
                    }
                    else
                    {
                        result.push_back(vector<int>{nums[a], nums[b], nums[left], nums[right]});
                        left++;
                        right--;
                        // 为c去重
                        while (left < right && nums[left] == nums[left - 1])
                        {
                            left++;
                        }
                        // 为d去重
                        while (left < right && nums[right] == nums[right + 1])
                        {
                            right--;
                        }
                    }
                }
            }
        }

        return result;
    }
};
```

