### 一、移除链表元素

[203. 移除链表元素 - 力扣（LeetCode）](https://leetcode.cn/problems/remove-linked-list-elements/)

[文章题解](https://programmercarl.com/0203.移除链表元素.html#算法公开课)

[视频题解](https://www.bilibili.com/video/BV18B4y1s7R9/)

#### 1、关键思想

1. 为链表设置**虚拟头结点**，使得头结点的删除与其他节点归一化；
2. 注意最后要返回**虚拟头结点的next节点**；

#### 2、代码

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
    ListNode* removeElements(ListNode* head, int val) {
        // 设置虚拟头结点
        ListNode *dummy_head = new ListNode(0, head);
        ListNode *cur_node = dummy_head;

        // 删除元素
        while (cur_node->next != nullptr)
        {
            if (cur_node->next->val == val)
            {
                ListNode *tmp = cur_node->next;
                cur_node->next = cur_node->next->next;
                delete tmp;
            }
            else
            {
                cur_node = cur_node->next;
            }
        }
        head = dummy_head->next;
        delete dummy_head;
        return head;
    }
};
```

### 3、注意点

1. C++的结构体中，`ListNode(int x, ListNode *next) : val(x), next(next)`是带有结构体**“成员初始化列表”**的构造函数；
2. new作为运算符可以放在构造函数之前，意味着调用相应的构造函数及其参数列表初始化该结构体类型的对象，并返回指向该对象的指针；例如`ListNode *dummy_head = new ListNode(0, head);`；

### 二、设计链表

[707. 设计链表 - 力扣（LeetCode）](https://leetcode.cn/problems/design-linked-list/submissions/537956653/)

[文字题解](https://programmercarl.com/0707.设计链表.html#思路)

[视频题解](https://www.bilibili.com/video/BV1FU4y1X7WD)

#### 1、注意点

1. 涉及到第n个节点的值时，注意判断n的合法性；

2. 在第n个节点前插入节点时，保证cur->next指向第n个节点（这样才能成功操作插入），分两步走;
    <img src="./day3_链表Part1.assets/image-20240607195857151.png" alt="image-20240607195857151" style="zoom:80%;" />

3. 注意在private中添加成员：

    ```c++
    ListNode *_dummy_head;
    int _size;
    ```

4. 提交时删除调试的cout代码，否则可能导致超时；

#### 2、代码实现

```c++
class MyLinkedList
{
public:
    struct ListNode
    {
        int val;
        ListNode *next;
        ListNode(int val, ListNode *next) : val(val), next(next){};
    };

    MyLinkedList()
    {
        _dummy_head = new ListNode(0, nullptr);
        _size = 0;
    }

    // 获取索引为index的链表节点的val
    // 如果index无效，返回-1
    int get(int index)
    {
        // cout << "get start:";
        // printList(_dummy_head);

        ListNode *cur = _dummy_head;
        if (index > _size - 1 || index < 0)
        {
            return -1;
        }
        while (index != 0)
        {
            cur = cur->next;
            index--;
        }

        // cout << "get end:";
        // cout << "get val:" << cur->next->val << " ";
        // printList(_dummy_head);
        return cur->next->val;
    }

    // 在头部插入值为val的节点
    void addAtHead(int val)
    {
        // cout << "addAtHead start:";
        // printList(_dummy_head);

        ListNode *new_node = new ListNode(val, nullptr);
        new_node->next = _dummy_head->next;
        _dummy_head->next = new_node;
        _size++;

        // cout << "addAtHead end:";
        // printList(_dummy_head);
    }

    // 在链表尾部插入元素
    void addAtTail(int val)
    {
        // cout << "addAtTail start:";
        // printList(_dummy_head);

        ListNode *cur = _dummy_head;
        ListNode *new_node = new ListNode(val, nullptr);
        while (cur->next != nullptr)
        {
            cur = cur->next;
        }
        cur->next = new_node;
        _size++;

        // cout << "addAtTail end:";
        // printList(_dummy_head);
    }

    // 在第index个节点前插入值为val的节点；
    // 如果index小于0或index大于链表长度，则直接返回空
    // 要保证cur->next指向index节点，才能利用cur插入节点
    void addAtIndex(int index, int val)
    {
        // cout << "addAtIndex start:";
        // printList(_dummy_head);

        if (index < 0 || index > _size)
        {
            return;
        }
        ListNode *cur = _dummy_head;
        ListNode *new_node = new ListNode(val, nullptr);
        while (index != 0)
        {
            cur = cur->next;
            index--;
        }
        new_node->next = cur->next;
        cur->next = new_node;
        _size++;

        // cout << "addAtIndex end:";
        // printList(_dummy_head);
    }

    // 删除第index个结点，如果下标无效，直接返回空
    void deleteAtIndex(int index)
    {
        // cout << "deleteAtIndex start:";
        // cout << "size:" << _size << endl;
        // printList(_dummy_head);
        if (index < 0 || index > _size - 1)
        {
            return;
        }
        ListNode *cur = _dummy_head;
        while (index != 0)
        {
            cur = cur->next;
            index--;
        }
        ListNode *tmp = cur->next;
        cur->next = cur->next->next;
        delete tmp;
        tmp = nullptr;
        _size--;

        // cout << "deleteAtIndex end:";
        // printList(_dummy_head);
    }

private:
    ListNode *_dummy_head;
    int _size;

    void printList(ListNode *head)
    {
        ListNode *cur = head;
        while (cur->next != nullptr)
        {
            cout << cur->next->val << "->";
            cur = cur->next;
        }
        cout << endl;
    }
};

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList* obj = new MyLinkedList();
 * int param_1 = obj->get(index);
 * obj->addAtHead(val);
 * obj->addAtTail(val);
 * obj->addAtIndex(index,val);
 * obj->deleteAtIndex(index);
 */
```

### 三、翻转链表

#### 1、主要思想

1. 双指针，注意tmp暂存节点；

#### 2、代码

```c++
class Solution
{
public:
    ListNode *reverseList(ListNode *head)
    {
        ListNode *cur = head;
        ListNode *pre = NULL;

        while (cur != nullptr)
        {
            ListNode *tmp = cur->next;
            cur->next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
};
```
