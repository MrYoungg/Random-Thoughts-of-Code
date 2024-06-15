### 四、两两交换链表中的节点

[24.两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

[文章题解](https://programmercarl.com/0024.两两交换链表中的节点.html#算法公开课)

[视频题解](https://www.bilibili.com/video/BV1YT411g7br/?vd_source=1e61d896f0ce15962eab39c0507f505d)

#### 1、解题思路

如下解法可以仅使用一个tmp值；

![image-20240608102357345](./day4_链表Part2.assets/image-20240608102357345.png)

#### 2、注意点

1. 可以将循环条件设置为`while(cur->next != nullptr && cur->next->next != nullptr)`；在C++中，&&运算符是从左至右运算的，且while循环有短路特性，若`cur->next==nullptr `，则不会再判断后续条件，因此不会导致空指针访问；

2. 也可以如下，但else分支一定要写；

    ```c++
    while(cur->next!=nullptr)
    {
        if(cur->next->next!=nullptr)
        {
            ...
        }
        else
        {
            break;//必须要有else语句的break，否则在奇数个节点时会陷入死循环；
        }
    }
    ```

    

#### 3、代码

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {

        ListNode* dummy_head = new ListNode(0,head);
        ListNode* cur = dummy_head;
        
        while(cur->next!=nullptr)
        {
            if(cur->next->next!=nullptr)
            {
                ListNode *tmp=cur->next->next;  // 暂存节点
                cur->next->next=tmp->next;      // 步骤1
                tmp->next=cur->next;            // 步骤2
                cur->next=tmp;                  // 步骤3
                
                cur=cur->next->next;             // 后移
            }
            else
            {
                break;//必须要有else语句的break，否则在奇数个节点时会陷入死循环；
            }
        }

        head=dummy_head->next;
        delete dummy_head;
        return head;
    }
};
```

### 五、删除链表的倒数第n个节点

[9.删除链表的倒数第N个节点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0019.删除链表的倒数第N个节点.html#算法公开课)

[链表遍历学清楚！ | LeetCode：19.删除链表倒数第N个节点-哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1vW4y1U7Gf/?vd_source=1e61d896f0ce15962eab39c0507f505d)

#### 1、思路

快慢指针，思维惯性是逐个遍历，但事实上可以自行添加标记点；

#### 2、注意点

1. 快指针要走n+1步，保证slow指向被删节点的前一个节点；
2. // 题目说明了：1 <= n <= size，因此不用考虑节点不存在的情况；

#### 3、代码

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution
{
public:
    ListNode *removeNthFromEnd(ListNode *head, int n)
    {
        ListNode *dummy_head = new ListNode(0, head);
        ListNode *fast = dummy_head;
        ListNode *slow = dummy_head;

        n += 1; // 快指针提前移动n+1步，使得slow指向被删节点的前一个节点；

        // 先移动快指针
        while (n-- && fast != nullptr)
        {
            fast = fast->next;
        }

        // 快慢指针再一起移动
        while (fast != nullptr)
        {
            slow = slow->next;
            fast = fast->next;
        }
        // 题目说明了：1 <= n <= size，因此不用考虑节点不存在的情况；
        slow->next = slow->next->next;

        head = dummy_head->next;
        return head;
    }
};
```

### 六、链表相交

[面试题 02.07. 链表相交 - 力扣（LeetCode）](https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/面试题02.07.链表相交.html#思路)

#### 1、思路

1. 遍历计算出两个链表的长度；
2. 将长链表的遍历指针与短链表头对齐；
3. 两个数组的指针同步开始遍历，找到相等的指针即找到相交的节点；
    <img src="./day4_链表Part2.assets/image-20240608171513651.png" alt="image-20240608171513651" style="zoom:80%;" />

#### 2、注意点

1. 两个链表谁长谁短需要一个if...else来区别；
2. 计算完链表长度后记得重置cur指针；

#### 3、代码

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode *curA = headA;
        ListNode *curB = headB;
        int lengthA = 0;
        int lengthB = 0;

        // 计算两个链表的长度
        while (curA != nullptr)
        {
            lengthA++;
            curA = curA->next;
        }
        while (curB != nullptr)
        {
            lengthB++;
            curB = curB->next;
        }

        //重置curA和curB！！
        curA = headA;
        curB = headB;

        // A较长
        if (lengthA > lengthB)
        {
            // 计算长度差
            int length_gap = lengthA - lengthB;

            // 将长链表的遍历起点与短链表头对齐
            while (length_gap--)
            {
                curA = curA->next;
            }
        }
        else // B较长
        {
            // 计算长度差
            int length_gap = lengthB - lengthA;

            // 将长链表的遍历起点与短链表头对齐
            while (length_gap--)
            {
                curB = curB->next;
            }
        }

        // 寻找相交节点
        while (curA != curB)
        {
            curA = curA->next;
            curB = curB->next;
            if (curA == nullptr || curB == nullptr) // 没有相交节点
            {
                return nullptr;
            }
        }
        return curA;
    }
};
```

### 七、环形链表

#### 1、思路

1. 如何判断有没有环：快慢指针，快指针速度为2，慢指针速度为1，如果快慢指针会相遇，则代表有环；
2. 如何找到环的入口
    1. 设如下变量
        ![img](./day4_链表Part2.assets/20220925103433.png)
    2. 快指针走的路程：`x + y + n (y + z)`，n代表在环中转的圈数；
    3. 慢指针走的路程：`x + y`；
    4. 可以得到等式：`(x + y) * 2 = x + y + n (y + z)`，化简为`x = (n - 1) (y + z) + z`；可以看出，x等于z加上n-1圈，因此，如果慢指针从相遇处继续前进，新的cur指针从头结点前进，它们最终必然会在环的入口节点相遇；

#### 2、注意点

下述代码可以在逻辑上优化， 减少代码量；（如26行所言）

#### 3、代码

```c++
class Solution
{
private:
    int CYCLE = 1;
    int NOCYCLE = 0;

public:
    ListNode *detectCycle(ListNode *head)
    {
        ListNode *fast = head;    // 快指针
        ListNode *slow = head;    // 慢指针
        int cycle_flag = NOCYCLE; // 链表环标志位

        // 判断是否存在环
        while (fast != nullptr && fast->next != nullptr)
        {
            fast = fast->next->next;
            slow = slow->next;
            if (fast == slow)
            {
                cycle_flag = CYCLE;
                break;
            }
        }
		
        //可以将有环部分代码并入到while循环中的if判断内部
        if (cycle_flag == NOCYCLE) // 没有环
        {
            return nullptr;
        }
        else // 有环
        {
            ListNode *cur = head;
            while (cur != slow) // slow从相遇处开始走，cur从head开始走，相遇处即为环的入口
            {
                cur = cur->next;
                slow = slow->next;
            }
            return cur;
        }
    }
};
```

