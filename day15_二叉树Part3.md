### 一、110.平衡二叉树

[110. 平衡二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/balanced-binary-tree/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0110.平衡二叉树.html#算法公开课)

#### 1、思路

1. 对于这类以根节点为判断基点的题目（必须从叶子节点一路遍历到根节点才能完成），都应该考虑后序遍历（左-右-中）；
2. 例如本题判断逻辑为：判断**每个根节点**的左右子树是不是平衡二叉树；对于**104.二叉树最大深度**，判断逻辑为：求**每个根节点**左右子树的最大深度；
3. 因为最终结果是返回到根节点，在此之前左右节点需要完成自身的判断，符合后序遍历的规律（左-右-中）；

#### 2、注意点

1. **递归三部曲：**
    1. 参数：当前节点（作为根节点）；返回值：如果左右子树高度差大于1，则向上返回-1（代表本子树不是平衡二叉树），否则向上返回本节点的高度（或者说本子树的深度）用于上层继续比较；
    2. 递归终止条件：遍历到空姐点，则返回高度为0；
    3. 递归调用：后序遍历，依次求左-右-中节点的高度；
2. **迭代解题思路：**定义一个函数getHigh(TreeNode* root)：求当前节点的高度（层序遍历求高度即可）；再后序遍历整棵树，在每个节点位置求左右子树高度差进行判断；

#### 3、代码

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left),
 * right(right) {}
 * };
 */
class Solution {
private:
    int getHigh(TreeNode* root) {
        // 递归终止条件:遍历到空节点，返回节点高度为0
        if (!root) {
            return 0;
        }

        // 递归调用，后序遍历：左-右-中
        // 若发现当前节点左右子树之差大于1，说明整棵树都不是平衡二叉树，则返回-1
        int left_high = getHigh(root->left); // 左
        // 接收到左节点的-1，返回-1给上层节点
        if (left_high == -1) {
            return -1;
        }

        int right_high = getHigh(root->right); // 右
        // 接收到右节点的-1，返回-1给上层节点
        if (right_high == -1) {
            return -1;
        }

        // 中，处理当前节点
        if (abs(left_high - right_high) >
            1) // 如果左右节点报告的高度差大于1，则向上返回-1
        {
            return -1;
        } 
        else // 否则返回当前节点的高度，给上层继续计算
        {
            cout<< "cur node high = "<<1+ max(left_high, right_high)<<endl;
            return 1 + max(left_high, right_high);
        }
    }

public:
    bool isBalanced(TreeNode* root) {
        int result = getHigh(root);
        return (result == -1) ? false : true;
    }
};
```

### 二、257.二叉树的所有路径

[257. 二叉树的所有路径 - 力扣（LeetCode）](https://leetcode.cn/problems/binary-tree-paths/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0257.二叉树的所有路径.html)

#### 1、思路

1. 由于该判断的基点是叶子节点（从根节点一路遍历到所有叶子节点，即完成所有路径的遍历），因此考虑前序遍历；
2. **递归三部曲：**
    1. **参数：**根节点、存储当前路径的int数组path，存储所有路径的string数组result；注意必须传入引用；    **返回值：**无；
    2. **终止条件：**遍历到叶子节点，则将当前path中的路径转化为string，放入result中；
    3. **递归调用**：前序遍历，中-左-右；

#### 2、注意点

1. string变量可以不初始化，string类的默认构造函数会将其初始化为空字符串；
2. 在递归终止之前就先处理中间节点（即当前节点），否则会漏掉叶子节点；
3. 递归终止条件：遍历到叶子节点终止，一定不能遍历到空节点才终止，否则每条路径都会输出两次；
4. 回溯：每次递归返回时都应该将path中的末尾值弹出，代表回到上层节点继续找另一条路径；
5. 节点不为空才继续递归，否则回溯时会弹出需要保留的当前节点；

#### 3、代码

1. **测试用例：**
    ![img](./day15_二叉树Part3.assets/paths-tree.jpg)
2. **输出结果：**

```
输出结果：
node:1
path:1 
node:2
path:1 2 
node:5
path:1 2 5 
1->2->5
node:3
path:1 3 
1->3
```

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left),
 * right(right) {}
 * };
 */
class Solution {
private:
    // 将path处理成符合result的输出格式
    string pathToStr(const vector<int>& path) 
    {
        string
            path_str; // 可以不初始化，string类的构造函数会将其初始化为空字符串；
        for (int i = 0; i < path.size(); i++) {

            // 最后一个数不添加"->"
            if (i == path.size() - 1) {
                path_str += to_string(path[i]);
            } else {
                path_str += (to_string(path[i]) + "->");
            }
        }
        return path_str;
    }

    // 一直遍历到底层则得到一条路径结果，考虑前序遍历：中-左-右
    void getPath(TreeNode* root, vector<int>& path, vector<string>& result) 
    {
        if (!root) // 如果根节点为空，直接返回空字符串
        {
            return;
        }

        // 在终止之前就要先处理中间节点（即当前节点），否则会漏掉叶子节点
        cout << "node:" << root->val << endl;
        path.push_back(root->val); // 中

        cout << "path:";
        for (auto i : path) {
            cout << i << " ";
        }
        cout << endl;

        // 递归终止条件：遍历到叶子节点终止；不能遍历到空节点才终止，否则每条路径都会输出两次
        if (!root->left && !root->right) {

            // 处理path
            string path_str = pathToStr(path);
            cout << path_str << endl;

            // 将每段path加入result中
            result.push_back(path_str);

            return;
        }

        if (root->left) // 左节点不为空才继续递归，否则回溯时会弹出当前值
        {
            getPath(root->left, path, result); // 左
            path.pop_back(); // 回溯，弹出当前路径的最后一个节点，从上一个节点开始找右侧的路径
        } 
        
        if(root->right)
        {
            getPath(root->right, path, result); // 右
            path.pop_back();                    // 回溯
        }
        
    }

public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<int> path;
        vector<string> result;

        getPath(root, path, result);

        return result;
    }
};
```

### 三、404.左叶子之和

[404. 左叶子之和 - 力扣（LeetCode）](https://leetcode.cn/problems/sum-of-left-leaves/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0404.左叶子之和.html)

#### 1、递归思路

1. 该题可以看成**逐层向上报告**左叶子之和，最后在根节点进行统计，因此是以根节点为基点的遍历，采用后序遍历；
2. **递归三部曲：**
    1. 参数和返回值：直接使用原题的函数；
    2. 终止条件：当前节点是叶子节点或空节点，return 0；
    3. 递归调用：后序遍历
        - 左节点：如果左节点就是左叶子，则向上报告自己的值；否则说明左节点拥有一棵左子树，则去找自己的左叶子和， 再向上报告；
        - 右节点：去找自己的左叶子和；
        - 中间节点：将左节点和右节点报告的值相加，返回；

#### 2、迭代思路

1. 迭代遍历每个节点，找到有左叶子的节点则返回；
2. 可以采用层序遍历，思路简单清晰；

#### 3、代码

1. **递归**

    ```
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left),
     * right(right) {}
     * };
     */
    class Solution {
    public:
        int sumOfLeftLeaves(TreeNode* root) {
    
            // 遇到空节点
            if (!root) {
                return 0;
            }
            // 遇到叶子节点
            if (!root->left && !root->right) {
                return 0;
            }
    
            int left_sum = 0;
            int right_sum = 0;
            // 逐层向上报告左叶子之和，考虑后序遍历：左-右-中
            // 左节点
            if (root->left && !root->left->left &&
                !root->left->right) // 如果发现我的的左节点是左叶子
            {
                left_sum = root->left->val; // 那么它报告给我的和就是它自己的值
            } else // 否则说明它是左子树，让它去找左叶子之和
            {
                left_sum =
                    sumOfLeftLeaves(root->left); // 让左节点去找自己的左叶子之和
            }
    
            // 右节点
            right_sum =
                sumOfLeftLeaves(root->right); // 让右节点去找自己的左叶子之和
    
            // 中间节点
            int sum = left_sum + right_sum; // 左右子树的左叶子和相加，向上报告
            return sum;
        }
    };
    ```

2. **迭代**

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left),
     * right(right) {}
     * };
     */
    class Solution {
    public:
        int sumOfLeftLeaves(TreeNode* root) {
    
            //层序遍历每个节点，如果找到左叶子就加入结果中
            queue<TreeNode*> que;
            int sum=0;
    
            if(root)
            {
                que.push(root);
            }
    
            while(!que.empty())
            {
                TreeNode* tmp_node=que.front();
                que.pop();
                if(tmp_node->left)
                {
                    que.push(tmp_node->left);
                }
                if(tmp_node->right)
                {
                    que.push(tmp_node->right);
                }
                
                if(tmp_node->left&&!tmp_node->left->left&&!tmp_node->left->right)
                {
                    sum+=tmp_node->left->val;
                }
            }
    
            return sum;
        }
    };
    ```

### 四、513.找树左下角的值

[513. 找树左下角的值 - 力扣（LeetCode）](https://leetcode.cn/problems/find-bottom-left-tree-value/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0513.找树左下角的值.html)

#### 1、递归思路

1. 无需考虑使用那种遍历，只需要先遍历左节点，再遍历右节点即可，没有中间节点的处理逻辑；
2. 定义一个最大深度max_depth和结果result，当遍历到比最大深度还深的叶子时，获取该叶子的值到result；
3. **递归三步曲**
    1. 入口参数：传入节点，传入节点的深度；
    2. 终止条件：遍历到比最大深度还深的叶子节点时，**更新最大深度**，获取节点值；
    3. 递归调用：先遍历左子树，再遍历右子树，注意递归返回之后，深度需要回溯到当前节点深度；
4. 注意：当前节点深度**等于**最大深度时，代表已经遍历过更左边的节点，无需获取当前节点的值；

#### 2、迭代思路

1. **层序遍历**，依次获取每行的元素；
2. 返回最后一行的首个元素；
3. 遍历过程中仅维护当前行的元素即可；

#### 3、代码

1. **递归**

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left),
     * right(right) {}
     * };
     */
    class Solution {
    private:
        int max_depth = 0;
        int result;
    
        // 入口参数：节点，当前节点深度
        void myGetLeftValue(TreeNode* root, int depth) {
    
            if (!root) {
                return;
            }
    
            // 递归终止条件：遍历到叶子节点，如果深度大于最大深度，则将该节点的值作为结果
            // 当前节点深度等于最大深度时，代表已经遍历过更左边的值，不返回该值
            if (!root->left && !root->right) {
                if (depth > max_depth) {
                    max_depth = depth; // 注意遇到叶子时更新最大深度
                    result = root->val;
                }
                return;
            }
    
            // 遍历左子树
            depth++;
            myGetLeftValue(root->left, depth);
            depth--; // 递归返回，深度回溯到当前节点
    
            // 遍历右子树
            depth++;
            myGetLeftValue(root->right, depth);
            depth--; // 递归返回，深度回溯到当前节点
        }
    
    public:
        int findBottomLeftValue(TreeNode* root) {
            myGetLeftValue(root, 1);
            return result;
        }
    };
    ```

    

2. **迭代**

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left),
     * right(right) {}
     * };
     */
    class Solution {
    public:
        int findBottomLeftValue(TreeNode* root) {
    
            // 层序遍历,返回二维数组最后一行的第一个元素
            queue<TreeNode*> que;
            int result=0;
            if (root) {
                que.push(root);
            }
    
            while (!que.empty()) {
                int level_size = que.size();
                vector<int> vec; // 用于保留每一行的元素，仅维护一行即可
                for (int i = 0; i < level_size; i++) {
                    TreeNode* tmp_node = que.front();
                    que.pop();
                    vec.push_back(tmp_node->val);
                    if (tmp_node->left) {
                        que.push(tmp_node->left);
                    }
                    if (tmp_node->right) {
                        que.push(tmp_node->right);
                    }
                }
                result=vec.front();
            }
    
            return result;
        }
    };
    ```

    

