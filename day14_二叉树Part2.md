### 一、226.翻转二叉树

[226. 翻转二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/invert-binary-tree/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0226.翻转二叉树.html)

#### 1、递归思路

1. 递归思想，逐个交换当前节点的左右节点；
2. 事实上无需考虑前、中、后序遍历的概念，因为递归访问左右节点的顺序是可以改变的；
3. 事实上树的层数就是递归的深度；
    ![翻转二叉树](./day14_二叉树Part2.assets/翻转二叉树.gif)

#### 2、迭代思路

- 参考**统一迭代法**的思路，标记中间节点在栈中的位置，在其出栈时处理左右子节点；

#### 3、注意点

1. 递归时可以优先判断左右节点是否为空，减少遍历的节点；
2. 注意仅有一个子节点为空时也要进行交换；

#### 4、代码

1. **递归**

    ```cpp
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
        void myInvert(TreeNode* node) {
            if (!node) {
                return;
            }
    
            swap(node->left, node->right);
            //加入判断后减少了最后一排空节点的遍历，对降低运行时间有帮助
            if(node->left)
            {
                myInvert(node->left);
            }
            if(node->right)
            {
                myInvert(node->right);
            }
            
        }
    
    public:
        TreeNode* invertTree(TreeNode* root) {
            myInvert(root);
            return root;
        }
    };
    ```

2. **迭代**

    ```c++
    	**
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
        TreeNode* invertTree(TreeNode* root) {
            // 前序：中-左-右，入栈顺序：右-左-中
            stack<TreeNode*> st;
            if (root) {
                st.push(root);
            }
            while (!st.empty()) {
                TreeNode* tmp_node = st.top();
                st.pop();
                if (tmp_node) {
                    if (tmp_node->right) {
                        st.push(tmp_node->right); // 右
                    }
                    if (tmp_node->left) {
                        st.push(tmp_node->left); // 左
                    }
                    st.push(tmp_node); // 中
                    st.push(nullptr);  // 标记
                } else {
                    TreeNode* tmp_node = st.top();
                    st.pop();
                    if(tmp_node->left||tmp_node->right)
                    {
                        swap(tmp_node->left,tmp_node->right);
                    }
                }
            }
            return root;
        }
    	};
    ```

### 二、对称二叉树

[101. 对称二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/symmetric-tree/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0101.对称二叉树.html)

#### 1、思路

1. 将树分为内侧和外侧来递归遍历；
2. **递归三部曲：**
    1. 递归函数的返回值和参数：递归函数的作用是判断当前节点的左右子树是否向对称；因此传入左右子节点，返回一个bool值；`bool mySymmetric(TreeNode*left,TreeNode*right)`
    2. 确定递归终止条件：左空右非空->false，左非空右空->false，左右非空但值不相等->false，左右均为空->true，左右节点不为空且值相等->递归判断下一棵子树；
    3. 递归调用：判断外侧子树应传入`(left->left,right->right)`，判断内侧子树应该传入`(left->right,right->left)`；
3. **迭代**：考虑用**栈**来存储每个节点，内侧和外侧两个节点一起出栈，判断是否相等，再将它们的左右孩子分别入栈，注意外侧和内侧孩子分别紧靠入栈；

#### 2、注意点

1. 注意左右节点均为空的判断情况，若采用||运算符，必须加上括号`(left||right)==0`，因为==的优先级比||高，不加括号会如下执行：`left||(right==0)`；
2. 对称判断的是**外侧子树**和**内侧子树**是否相等；

#### 3、代码

##### （1）递归

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    bool mySymmetric(TreeNode*left,TreeNode*right)
    {
        if(left==nullptr&&right!=nullptr)//左空右非空
        {
           return false; 
        }
        else if(right==nullptr&&left!=nullptr)//右空左非空
        {
            return false; 
        }
        // else if(left==nullptr&&right==nullptr)//左右都为空
        else if((left||right)==0)//必须加括号，||的优先级低于==
        {
            // bool b=left||right;
            // cout<<"left||right="<<b<<endl;
            return true;
        }
        else if(left->val!=right->val)//左右不为空，但数值不相等
        {
            return false;
        }

        //左右数值相等，此时递归处理左右节点的子树
        bool outside=mySymmetric(left->left,right->right);
        bool inside=mySymmetric(left->right,right->left);
        bool isSame=outside && inside;
        return isSame;
    }
public:
    bool isSymmetric(TreeNode* root) {
        bool result;
        //题目条件中节点数量大于1，不可能是空树，不用判断
        result=mySymmetric(root->left,root->right);
        return result;
    }
};
```

##### （2）迭代

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
    bool isSymmetric(TreeNode* root) {

        stack<TreeNode*> st;

        if (root) {
            st.push(root->left);
            st.push(root->right);
        }

        while (!st.empty()) {
            TreeNode* left_node = st.top();
            st.pop();
            TreeNode* right_node = st.top();
            st.pop();

            // 两节点均为空
            if (!left_node && !right_node) {
                continue;
            } 
            // 一空一非空，或两节点非空但值不相等
            else if ((!left_node) || (!right_node) ||
                       (left_node->val != right_node->val)) {
                return false;
            }

            // 入栈
            st.push(left_node->left);   // 外侧
            st.push(right_node->right); // 外侧
            st.push(left_node->right);  // 内侧
            st.push(right_node->left);  // 内侧
        }
        return true;
    }
};
```

### 三、104.二叉树的最大深度

[104. 二叉树的最大深度 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0104.二叉树的最大深度.html#算法公开课)

#### 1、思路

1. 递归：后序遍历求高度即可；
2. 迭代：层序遍历求深度；

#### 2、代码

##### （1）递归——后序遍历

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root)
        {
            return 0;
        }

        int depth=0;
        //后序遍历：左-右-中
        int left_depth=maxDepth(root->left);
        int right_depth=maxDepth(root->right);
        depth=1+max(left_depth,right_depth);

        return depth;
    }
};
```

##### （2）迭代——层序遍历

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode* root) {
        queue<TreeNode*> que;
        int depth=0;

        if(root)
        {
            que.push(root);
        }

        while(!que.empty())
        {
            int size=que.size();
            for(int i=0;i<size;i++)
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
            }
            depth++;
        }
        return depth;
    }
};
```

### 四、111.二叉树的最小深度

[111. 二叉树的最小深度 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0111.二叉树的最小深度.html)

#### 1、思路

1. 递归：后序遍历，找到高度的最小值；
2. 迭代：层序遍历，找到左右节点都为空的位置时，返回当前深度；

#### 2、注意点

1. 最小深度是指根节点离最近的叶子节点的距离；
2. 注意如果有一侧子树为空，则那一侧不计入根节点的最小深度中；
3. 对每棵子树都是如此；
    <img src="./day14_二叉树Part2.assets/111.二叉树的最小深度.png" alt="111.二叉树的最小深度" style="zoom:50%;" />

#### 3、代码

##### （1）递归

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
    int minDepth(TreeNode* root) {
        
        if(!root)
        {
            return 0;
        }
        
        int depth=0;
        int left_depth=minDepth(root->left);  // 遍历左子树
        int right_depth=minDepth(root->right);// 遍历右子树
        
        // 注意树的最小深度是根节点到最近的叶子节点的距离
        // 左子树为空但右子树不为空时，取右子树的最小高度
        if(!root->left&&root->right)
        {
            depth=1+right_depth;
        }
        // 右子树为空但左子树不为空时，取左子树的最小高度
        else if(!root->right&&root->left)
        {
            depth=1+left_depth;
        }
        // 左右子树均不为空，则取左右高度的最小值
        else
        {
            depth=1+min(left_depth,right_depth);
        }
        return depth;
    }
};
```

##### （2）迭代

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
    int minDepth(TreeNode* root) {
        queue<TreeNode*> que;
        int depth = 0;

        if (root) {
            que.push(root);
        }
        int level=0;
        while (!que.empty()) {
            depth++;
            int size = que.size();
            for (int i = 0; i < size; i++) {
                TreeNode* tmp_node = que.front();
                que.pop();
                if (tmp_node->left) {
                    que.push(tmp_node->left);
                }
                if (tmp_node->right) {
                    que.push(tmp_node->right);
                }
                if (!tmp_node->left && !tmp_node->right) {
                    return depth;
                }
            }
        }
        return depth;
    }
};
```

