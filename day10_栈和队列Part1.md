### 一、232.用栈实现队列

[232. 用栈实现队列 - 力扣（LeetCode）](https://leetcode.cn/problems/implement-queue-using-stacks/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0232.用栈实现队列.html)

#### 1、思路

1. 栈和队列最大的不同就是出栈和出队顺序不同；
2. 初始化两个栈，一个input栈，一个output栈；
3. 当需要出队时，从input栈将所有数据push到output栈，再从output栈出栈，即可将先入的元素先出；
    <img src="./day10_栈和队列Part1.assets/232.用栈实现队列版本2.gif" alt="232.用栈实现队列版本2" style="zoom:67%;" />

#### 2、注意点

1. `stack<int> inStack; 和 stack<int> outStack;`应该定义为MyQueue类的成员变量；
2. stack.pop()仅移除栈中的数据，并没有返回值；stack.top()可以返回栈顶的值；
3. 当输出栈**不为空**时，先将输出栈中的元素输出，此时不要将输入栈中的数据转移到输出栈；

#### 3、代码

```c++
/*
 * @lc app=leetcode.cn id=232 lang=cpp
 *
 * [232] 用栈实现队列
 */

// @lc code=start
#include <iostream>
#include <stack>
using namespace std;

class MyQueue
{
public:
    stack<int> inStack;
    stack<int> outStack;

    // 构造函数
    MyQueue()
    {
    }

    // 将元素推入队尾
    void push(int x)
    {
        inStack.push(x);
        cout << "push:" << inStack.top() << endl;
    }

    // 将队首的元素出队，并返回其值
    int pop()
    {
        // 当输出栈为空时，将输入栈中的数据全部压入输出栈中
        if (outStack.empty())
        {
            while (!inStack.empty())
            {
                // stack.pop()仅移除栈中的数据，并没有返回值
                // stack.top()可以返回栈顶的值
                outStack.push(inStack.top());
                inStack.pop(); // 压入输出栈中的元素要在输入栈中删除，为下次入队留出空间
            }
        }
        int result = outStack.top();
        outStack.pop();
        cout << "pop:" << result << endl;
        return result;
    }

    // 返回队首元素
    int peek()
    {
        // 当输出栈为空时，将输入栈中的数据全部压入输出栈中
        if (outStack.empty())
        {
            while (!inStack.empty())
            {
                // stack.pop()仅移除栈中的数据，并没有返回值
                // stack.top()可以返回栈顶的值
                outStack.push(inStack.top());
                inStack.pop(); // 压入输出栈中的元素要在输入栈中删除，为下次入队留出空间
            }
        }
        int result = outStack.top();
        cout << "peek:" << result << endl;
        return result;
    }

    // 判断队列是否为空
    bool empty()
    {
        if (inStack.empty() && outStack.empty())
        {
            cout << "non empty" << endl;
            return true;
        }
        else
        {
            cout << "empty" << endl;
            return false;
        }
    }
};

```

### 二、225.用队列实现栈

[225. 用队列实现栈 - 力扣（LeetCode）](https://leetcode.cn/problems/implement-stack-using-queues/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0225.用队列实现栈.html#思路)

#### 1、思想

1. 可以用双队列实现，但和栈实现队列有本质不同，因为无论在两个队列之间如何转移数据，数据流的顺序都不会改变，因此另一队列事实上仅仅是用于备份元素；
    <img src="./day10_栈和队列Part1.assets/225.用队列实现栈.gif" alt="225.用队列实现栈" style="zoom:67%;" />
2. 事实上单队列即可实现，将队首元素循环加入到队尾，循环找到要出栈的元素即可；

#### 2、注意点

#### 3、代码

1. top()获取栈顶元素的时候不应该破坏栈的结构,将推到队首的元素获取后还应该再放回队尾；

```c++
/*
 * @lc app=leetcode.cn id=225 lang=cpp
 *
 * [225] 用队列实现栈
 */

// @lc code=start
#include <iostream>
#include <queue>
using namespace std;

class MyStack
{
public:
    queue<int> que;
    MyStack()
    {
    }

    void push(int x)
    {
        que.push(x);
    }

    // 移除并返回栈顶元素
    int pop()
    {
        for (int i = 0; i < que.size() - 1; i++)
        {
            int tmp = que.front();
            que.pop();
            que.push(tmp);
        }
        int result = que.front();
        que.pop();
        return result;
    }

    int top()
    {
        for (int i = 0; i < que.size() - 1; i++)
        {
            int tmp = que.front();
            que.pop();
            que.push(tmp);
        }
        int result = que.front();

        // top()获取栈顶元素的时候不应该破坏栈的结构,将推到队首的元素获取后再放回队尾
        int tmp = que.front();
        que.pop();
        que.push(tmp);

        return result;
    }

    bool empty()
    {
        return que.empty();
    }
};

```

### 三、20.有效的括号

[20. 有效的括号 - 力扣（LeetCode）](https://leetcode.cn/problems/valid-parentheses/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0020.有效的括号.html#算法公开课)

#### 1、思路

1. 检查**括号的闭合性**是栈的典型应用，编译器检查代码的括号是否闭合就是用的栈逻辑；
2. 对于每一种括号，先括的后闭合，后括的先闭合，因此符合栈先进后出的特点；
3. 事实上，像这样特性的还有函数参数的压栈（后调用的函数必然先返回），中断的压栈（后进入的中断必然先返回）等，这些场景之所以都用**栈**这种数据结构，就是因为符合先进后出的特点；
4. 检测左括号时就将右括号入栈，检测到不是左括号时（题目说了字符串只有左右括号组成），top检查栈顶元素，相等则出栈，否则说明左右括号不匹配
    <img src="./day10_栈和队列Part1.assets/20200809155107397.png" alt="括号匹配2" style="zoom:67%;" />
5. 如果数组还未遍历完，栈为空栈，则说明有多余的右括号；
    <img src="./day10_栈和队列Part1.assets/20200809155115779.png" alt="括号匹配3" style="zoom:50%;" />
6. 如果数组遍历完后，栈不为空栈，则说明有多余的左括号；
    <img src="./day10_栈和队列Part1.assets/2020080915505387.png" alt="括号匹配1" style="zoom:50%;" />

#### 2、注意点

1. 事实上只有三种情况是不匹配的：
    1. 有多余的左括号；
    2. 有多余的右括号；
    3. 括左右号不匹配；

#### 3、代码

```c++
/*
 * @lc app=leetcode.cn id=20 lang=cpp
 *
 * [20] 有效的括号
 */

// @lc code=start
#include <iostream>
#include <string>
#include <stack>
using namespace std;

class Solution
{
public:
    bool isValid(string s)
    {
        stack<int> brackets_stack;
        for (int i = 0; i < s.size(); i++)
        {
            if (s[i] == '{')
            {
                brackets_stack.push('}');
            }
            else if (s[i] == '[')
            {
                brackets_stack.push(']');
            }
            else if (s[i] == '(')
            {
                brackets_stack.push(')');
            }
            else
            {
                if (!brackets_stack.empty() && brackets_stack.top() == s[i])
                {
                    brackets_stack.pop();
                }
                // 情况3:符号不匹配
                // 情况2:有多余的右符号
                else
                {
                    return false;
                }
            }
        }

        // 字符串遍历结束后，检查栈是否空，非空则代表情况1:有多余的右符号
        return brackets_stack.empty();
    }
};
// @lc code=end

```

### 四、1047. 删除字符串中的所有相邻重复项

[20. 有效的括号 - 力扣（LeetCode）](https://leetcode.cn/problems/valid-parentheses/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/1047.删除字符串中的所有相邻重复项.html#算法公开课)

#### 1、思路

1. 使用栈的完美场景；
2. 遍历字符串，依次入栈，如果发现要入栈的字符和栈顶字符相同，则删除当前（第i个）元素和前一个（i-1）元素；

#### 2、注意点

- 注意stack.erase()的使用；

#### 3、代码

```c++
/*
 * @lc app=leetcode.cn id=1047 lang=cpp
 *
 * [1047] 删除字符串中的所有相邻重复项
 */

// @lc code=start
#include <iostream>
#include <string>
#include <stack>
using namespace std;

class Solution
{
public:
    string removeDuplicates(string s)
    {
        stack<int> letter_stack;
        for (int i = 0; i < s.size(); i++)
        {
            if (!letter_stack.empty() && letter_stack.top() == s[i])
            {
                letter_stack.pop();
                cout << "i:" << i << endl;
                s.erase(i - 1, 2); // 删除从i-1开始的2个字符；
                i -= 2;
            }
            else
            {
                cout << "push:" << s[i] << endl;
                letter_stack.push(s[i]);
            }
        }
        return s;
    }
};
// @lc code=end

```

