### 一、530.二叉搜索树的最小绝对差

#### 1、思路

1. 看到二叉搜索树，就应该想到其中序遍历是有序数组；
2. 想象一下在有序数组中寻找最小差值，是不是遍历一遍就行了？
3. 差值最小的两个数在有序序列中必然是相邻的，因此一个pre指针存上一个值，与当前值做差即可；
4. 用一个值min_diff存最小差值，找到更小的差值时更新；

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
private:
    TreeNode* pre = nullptr;
    int min_diff = INT_MAX;
    // 二叉搜索树的中序遍历是升序
    // 差值最小的两值必然相邻
    void traversal(TreeNode* root) {
        if (!root) {
            return;
        }

        // 中序遍历
        traversal(root->left); // 左

        // 中间节点处理逻辑
        if (pre && root->val != pre->val && (root->val - pre->val) < min_diff)
        {
            min_diff=root->val - pre->val;
        }
        pre=root;

        traversal(root->right); // 右

        return;
    }

public:
    int getMinimumDifference(TreeNode* root) {
        traversal(root);
        return min_diff;
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
    int getMinimumDifference(TreeNode* root) {
        // 中序遍历，迭代实现
        stack<TreeNode*> st;
        TreeNode* cur = root;
        TreeNode* pre = nullptr;
        int min_diff = INT_MAX;
        while (cur || !st.empty()) {

            // 指向的节点不为空时，将其入栈
            if (cur) {
                st.push(cur);
                // 继续遍历左子树
                cur = cur->left;
            } 

            // 指向的节点为空时，将栈中的节点出栈，准备处理
            else {
                cur = st.top();
                st.pop();
                // 处理当前值
                if (pre && cur->val != pre->val &&
                    cur->val - pre->val < min_diff) {
                    // 对于二叉搜索树的中序遍历，当前值必然比前一值大
                    min_diff = cur->val - pre->val;
                }
                pre = cur;

                // 处理完毕，继续遍历右子树
                cur=cur->right;
            }
        }

        return min_diff;
    }
};
```

### 二、501.二叉搜索树中的众数

#### 1、思路

1. 仍然考虑**中序遍历**获得的有序序列，众数必然在序列中连续出现；
2. max_freq记录已知最大频率，root_freq记录当前值已经出现的频率，pre指针指向遍历中root的前一个节点；
3. 比较pre和root的值，相等表明root_freq++，若root_freq==max_freq，代表找到频率相同的数，加入结果数组中；
4. 若root_freq>max_freq，代表找到频率更大的数（新众数），**将原本数组中的值全部清除**，加入新的众数；

#### 2、注意点

1. 注意在找到新众数后，要将原数组clear；
2. 注意当root_freq>max_freq时，要更新max_freq；
3. 注意每次处理完中间节点，要更新pre节点为root节点；

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
    vector<int> result;
    int max_freq = 0;
    int root_freq = 0;
    TreeNode* pre = nullptr;
    void myFindMode(TreeNode* root) {
        if (!root) {
            return;
        }

        myFindMode(root->left); // 左

        // 处理中间节点
        // pre为空，说明刚开始遍历第一个元素
        // 当前值不等于前一值，说明遍历到新值
        // 上述两种情况，root_freq都重新赋值为1
        if (!pre || root->val != pre->val) {
            root_freq = 1;
        }
        // 当前值和前一值相等，当前频率++
        else if (root->val == pre->val) {
            root_freq++;
        }
        cout<<"cur:"<<root->val<<endl;
        cout<<"root_freq:"<<root_freq<<" ";
        cout<<"max_freq:"<<max_freq<<endl;

        // 当前数值出现频率等于最大频率，加入众数中
        if (root_freq == max_freq) {
            result.push_back(root->val);
        }
        // 当前数值出现频率大于最大频率，原先的众数全部清除，获得新的众数
        if (root_freq > max_freq) {
            result.clear();
            result.push_back(root->val);
            max_freq=root_freq; // 记得更新最大频率
        }
        pre = root; // 记得更新pre

        myFindMode(root->right);
    }

public:
    vector<int> findMode(TreeNode* root) {
        myFindMode(root);
        return result;
    }
};
```

### 三、236. 二叉树的最近公共祖先

#### 1、思路

1. 遍历判断本节点的子树中是否有p、q，将结果向逐层上返回；
2. 以根节点作为判断标准，因此考虑后序遍历；

#### 2、注意点

1. 容易考虑到的情况
    <img src="./day19_二叉树Part6.assets/20220922173502.png" alt="img" style="zoom: 50%;" />
2. 注意考虑：p和q之间本身就互为祖先和孩子的情况；
3. 由于一个节点可以作为其自身的祖先，因此若p就是q的祖先，则p就是最大公共祖先（互换同理）；
4. 此时都不需要继续向下找q，向上返回p即可；（逻辑已被包含在正常情况的处理中）
    <img src="./day19_二叉树Part6.assets/20220922173530.png" alt="img" style="zoom: 50%;" />

#### 3、代码

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    // 注意考虑：p和q之间本身就互为祖先和孩子的情况
    // 由于一个节点可以作为其自身的祖先，因此若p就是q的祖先，则p就是最大公共祖先（互换同理）
    // 此时都不需要继续向下找q，向上返回p即可
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // 思路：判断子树中是否有p、q，将结果向上返回
        // 以根节点为判断基准，考虑后序遍历

        if (!root) 
        {
            return nullptr;
        }
        if (root == p || root == q) // 找到p、q，则向上返回找到的节点
        {
            return root;
        }
        
        TreeNode* left_node=lowestCommonAncestor(root->left,p,q); // 左
        TreeNode* right_node=lowestCommonAncestor(root->right,p,q); // 右

        // 中间节点处理逻辑
        if(left_node&&right_node)// 左右节点返回值都不为空，说明当前节点就是最近公共祖先
        {
            return root;
        }
        // 哪个子节点返回的节点不为空，则继续向上返回该节点
        else if(left_node&&!right_node)
        {
            return left_node;
        }
        else if(!left_node&&right_node)
        {
            return right_node;
        }
        // 两个子节点返回的节点都为空
        else
        {
            return nullptr;
        }

    }
};
```