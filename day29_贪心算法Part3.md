### 十、135. 分发糖果

[135. 分发糖果 - 力扣（LeetCode）](https://leetcode.cn/problems/candy/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0135.分发糖果.html#算法公开课)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    int candy(vector<int>& ratings) {
        int result = 0;
        vector<int> candy(ratings.size(),
                          1); // 将糖果数组初始化为1，每个人先分一个

        // 正向遍历，i+1的评分高于i时，为i+1添糖果
        for (int i = 0; i < ratings.size() - 1; i++) {
            // 正向遍历时大家的糖果都是1，无需判断糖果之间的关系
            if ((ratings[i + 1] > ratings[i])) {
                candy[i + 1] = candy[i] + 1;
            }
        }

        // 反向遍历，比较i-1和i
        for(int i=ratings.size()-1;i>0;i--){
            // 反向遍历时糖果数已经改变过一次，i-1的糖果数要取candy[i]+1和candy[i-1]之间的较大值，才能同时满足左右两种情况
            if(ratings[i-1]>ratings[i]){
                candy[i-1]=max(candy[i]+1,candy[i-1]);
            }
        }

        // 计算总的糖果数
        for(auto i:candy){
            result+=i;
        }

        return result;
    }
};
```

### 十一、860.柠檬水找零

[860. 柠檬水找零 - 力扣（LeetCode）](https://leetcode.cn/problems/lemonade-change/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0860.柠檬水找零.html#算法公开课)

#### 1、思路

#### 2、注意点

1. 注意顾客的购买是有顺序的，要按顺序满足找零要求；

#### 3、代码

```c++
class Solution {
public:
    int dollar_5 = 0;
    int dollar_10 = 0;
    int dollar_20 = 0;

    void printDollar(void) {
        cout << "dollar_5:" << dollar_5 << endl;
        cout << "dollar_10:" << dollar_10 << endl;
        cout << "dollar_20:" << dollar_20 << endl;
        cout << endl;
    }

    bool lemonadeChange(vector<int>& bills) {

        for (int i = 0; i < bills.size(); i++) {
            // cout << "i:" << i << endl;
            if (bills[i] == 5) {
                dollar_5++;
                // printDollar();
            } else if (bills[i] == 10) {
                dollar_10++;
                // printDollar();
                if (dollar_5 <= 0) {
                    // cout << "10 false" << endl;
                    return false;
                } else {
                    dollar_5--;
                }
                // printDollar();
            } else {
                dollar_20++;
                // printDollar();
                // 必须优先用10块找零20，尽可能把5块留下用来找零10块
                if (dollar_10 >= 1 && dollar_5 >= 1) {
                    dollar_10 -= 1;
                    dollar_5 -= 1;
                } else if (dollar_5 >= 3) {
                    dollar_5 -= 3;
                } else {
                    // cout << "20 false";
                    return false;
                }
                // printDollar();
            }
        }
        return true;
    }
};
```

### 十二、406.根据身高重建队列

[406. 根据身高重建队列 - 力扣（LeetCode）](https://leetcode.cn/problems/queue-reconstruction-by-height/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0406.根据身高重建队列.html#总结)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    void printPeople(vector<vector<int>>& people) {
        for (auto i : people) {
            cout << "[" << i[0] << "," << i[1] << "]" << endl;
        }
    }

    static bool cmp(vector<int> a, vector<int> b) {
        if (a[0] == b[0]) {
            return a[1] < b[1];
        }

        return a[0] > b[0];
    }

    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {

        list<vector<int>> list_result; // 用链表实现，插入的时间复杂度更低

        // 先按照身高h从高到低排序
        sort(people.begin(), people.end(), cmp);
        printPeople(people);

        // 再按照k将当前的人插入到相应位置
        for (int i = 0; i < people.size(); i++) {
            cout << i;
            // 无论k是否符合，每一次都要插入，否则会漏掉某些元素
            int pos = people[i][1];
            list<vector<int>>::iterator it=list_result.begin();
            while(pos--)
            {
                it++;
            }
            list_result.insert(it, people[i]);
        }
        return vector<vector<int>>(list_result.begin(),list_result.end());
    }
};
```

### 