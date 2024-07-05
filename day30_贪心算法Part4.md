### 十三、452. 用最少数量的箭引爆气球

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    static bool cmp(vector<int> a, vector<int> b) { return a[0] < b[0]; }

    // void printPoints(vector<vector<int>>& points) {
    //     cout << "[";
    //     for (auto i : points) {
    //         cout << "[" << i[0] << "," << i[1] << "]"
    //              << " ";
    //     }
    //     cout << "]";
    //     cout << endl;
    // }

    int findMinArrowShots(vector<vector<int>>& points) {
        // 先按左边界升序排列
        sort(points.begin(), points.end(), cmp);
        // printPoints(points);

        // 寻找能一箭引爆的重叠气球
        int result = 1; // 至少需要一箭，初始化为1
        for (int i = 0; i < points.size()-1; i++) {
            // 气球i+1和i所在组的气球不重叠，则增加箭数量
            if (points[i][1] < points[i+1][0]) { 
                result++;
            }
            // 气球i+1和i挨着，则将i+1加入该组，并修改该组的右边界
            else{ 
                points[i+1][1]=min(points[i][1],points[i+1][1]);
            }
        }

        return result;
    }
};
```

### 十四、435. 无重叠区间

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    static bool cmp(vector<int> a, vector<int> b)
    {
        return a[0]<b[0];
    }

    void printIntervals(vector<vector<int>>& intervals)
    {
        for(auto i:intervals)
        {
            cout<<"["<<i[0]<<","<<i[1]<<"]"<<" ";
        }
        cout<<endl;
    }

    int eraseOverlapIntervals(vector<vector<int>>& intervals) {

        // 排序
        sort(intervals.begin(),intervals.end(),cmp);
        // printIntervals(intervals);

        int result = 0;

        for (int i = 0; i < intervals.size()-1; i++) {
            // 找到重叠的区间
            if(intervals[i][1]>intervals[i+1][0]){
                result++;
                // 更新右边界
                intervals[i+1][1]=min(intervals[i][1],intervals[i+1][1]);
            }
        }
        
        return result;
    }
};
```

### 十五、763.划分字母区间

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
class Solution {
public:
    void printVec(const vector<int> &vec)
    {
        for(auto i:vec)
        {
            cout<<i<<" ";
        }
        cout<<endl;
    }

    vector<int> partitionLabels(string s) {
        vector<int> last_position(26,0); // 每个字母最后出现的位置
        vector<int> result;
        int str_length=0;
        int cur_str_edge=0;

        // 统计每个字母最后出现的位置
        for(int i=0;i<s.size();i++)
        {
            int index=s[i]-'a';
            last_position[index] = max(last_position[index],i);
        }
        printVec(last_position);

        // 找到每个分割字符串的长度
        for(int i=0;i<s.size();i++)
        {
            int index = s[i] - 'a';

            cur_str_edge=max(cur_str_edge,last_position[index]);
            str_length++;

            if(i==cur_str_edge)
            {
                result.push_back(str_length);
                str_length=0;
                if(i!=s.size()-1)
                    cur_str_edge=last_position[(int)(s[i+1]-'a')];
            }

        }

        return result;
    }
};
```

