### 二十五、SPFA算法（Bellman_Ford算法 - 队列优化）

[94. 城市间货物运输 I (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1152)

[代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0094.城市间货物运输I-SPFA.html#模拟过程)

#### 1、思路 

- 事实上每次都松弛所有边是很浪费的，可以看到在松弛的过程中有**很多次松弛都没有改变minDistance的值**，这些松弛是无效的；
- 观察后容易发现，如果在某次松弛中**节点Node的minDistance[Node]发生了更新**，则在下一次松弛时，**Node节点指向的节点有可能被更新**，我们**只需要松弛所有Node的每条出边即可**；

#### 2、注意点

- 仅当minDistance[Node]改变时，才将Node加入队列中；
- 重复加入也无妨，因为下一次从队列中取出Node时，minDistance就会维持不变，其相连节点就不会被加入队列中，不会陷入死循环；

#### 3、代码

```c++
#include <climits>
#include <iostream>
#include <list>
#include <queue>
#include <vector>
using namespace std;

#define MAX_EdgeWeight 101
#define NotUpdated false
#define BeenUpdated true

typedef int Node;
struct map_NodeAndInEdge {
    Node node;
    int inEdgeWeight;
};

void getInput(int &nodeNum, int &edgeNum, vector<list<map_NodeAndInEdge>> &map)
{
    cin >> nodeNum >> edgeNum;
    int fromNode = 0;
    int edgeWeight = 0;
    map_NodeAndInEdge curNode;
    map.resize(nodeNum + 1);
    for (int i = 0; i < edgeNum; i++) {
        cin >> fromNode >> curNode.node >> curNode.inEdgeWeight;
        map[fromNode].push_back(curNode);
    }
}

void relaxEdge(const vector<list<map_NodeAndInEdge>> map,
               vector<int> &minDistance, queue<int> &updatedNode_Queue,
               vector<bool> &isUpdated)
{
    Node updatedNode = updatedNode_Queue.front();
    updatedNode_Queue.pop();
    for (auto connecteNode : map[updatedNode]) {

        if (minDistance[updatedNode] + connecteNode.inEdgeWeight <
            minDistance[connecteNode.node]) {

            minDistance[connecteNode.node] =
                minDistance[updatedNode] + connecteNode.inEdgeWeight;

            updatedNode_Queue.push(connecteNode.node);
        }
    }
}

void printResult(const vector<int> &minDistance)
{
    if (minDistance.back() == MAX_EdgeWeight) {
        cout << "unconnected";
        return;
    }
    cout << minDistance.back();
}

void solve()
{
    int nodeNum = 0;
    int edgeNum = 0;
    vector<list<map_NodeAndInEdge>> map;
    getInput(nodeNum, edgeNum, map);

    vector<int> minDistance(nodeNum + 1, MAX_EdgeWeight);
    minDistance[1] = 0;

    queue<int> updatedNode_Queue;
    updatedNode_Queue.push(1);

    vector<bool> isUpdated(nodeNum + 1, NotUpdated);
    isUpdated[1] = BeenUpdated;

    while (!updatedNode_Queue.empty()) {
        relaxEdge(map, minDistance, updatedNode_Queue, isUpdated);
    }

    printResult(minDistance);
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```

### 二十六、Bellman_Ford算法-判断负权回路

[95. 城市间货物运输 II (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1153)

[代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0095.城市间货物运输II.html#拓展)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

#define ThereIsNegativeLoop 1
#define NoNegativeLoop 0

typedef int Node;
struct Edge {
    Node fromNode;
    Node toNode;
    int edgeWeight;
};

void getInputAndInit(int &nodeNum, int &edgeNum, vector<Edge> &edges,
                     vector<int> &minDistance)
{
    cin >> nodeNum >> edgeNum;
    minDistance.resize(nodeNum + 1, INT_MAX);
    minDistance[1] = 0;
    Edge curEdge;
    for (int i = 0; i < edgeNum; i++) {
        cin >> curEdge.fromNode >> curEdge.toNode >> curEdge.edgeWeight;
        edges.push_back(curEdge);
    }
}

void relaxEdge(const vector<Edge> &edges, vector<int> &minDistance)
{
    for (Edge curEdge : edges) {

        long long originDistance = minDistance[curEdge.toNode];
        long long newDistance =
            minDistance[curEdge.fromNode] + curEdge.edgeWeight;

        bool thereIsShorterPath = (minDistance[curEdge.fromNode] != INT_MAX) &&
                                  (newDistance < originDistance);

        if (thereIsShorterPath) {
            minDistance[curEdge.toNode] = (int)newDistance;
        }
    }
}

bool checkNegativeloop(const vector<Edge> &edges, vector<int> &minDistance)
{
    for (Edge curEdge : edges) {

        long long originDistance = minDistance[curEdge.toNode];
        long long newDistance =
            minDistance[curEdge.fromNode] + curEdge.edgeWeight;

        bool thereIsShorterPath = (minDistance[curEdge.fromNode] != INT_MAX) &&
                                  (newDistance < originDistance);

        if (thereIsShorterPath) {
            return ThereIsNegativeLoop;
        }
    }
    return NoNegativeLoop;
}

void printResult(const bool NegativeLoopFlag, const vector<int> &minDistance)
{
    if (NegativeLoopFlag == ThereIsNegativeLoop) {
        cout << "circle";
    }
    else if (minDistance.back() == INT_MAX) {
        cout << "unconnected";
    }
    else {
        cout << minDistance.back();
    }
}

void solve()
{
    int nodeNum = 0;
    int edgeNum = 0;
    vector<Edge> edges;
    vector<int> minDistance;
    getInputAndInit(nodeNum, edgeNum, edges, minDistance);

    // for (auto i : edges) {
    //     cout << i.fromNode << " " << i.toNode << " " << i.edgeWeight <<
    //     endl;
    // }

    int loops = nodeNum - 1;
    while (loops--) {
        relaxEdge(edges, minDistance);
    }

    bool NegativeLoopFlag = checkNegativeloop(edges, minDistance);

    printResult(NegativeLoopFlag, minDistance);
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```

### 二十七、BellmanFord-单源有限最短路径

[96. 城市间货物运输 III (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1154)

[代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0096.城市间货物运输III.html#思路)

#### 1、思路

#### 2、注意点

#### 3、代码

```c++
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

typedef int Node;
struct Edge {
    Node fromNode;
    Node toNode;
    int edgeWeight;
};

void getInput(int &nodeNum, int &edgeNum, vector<Edge> &edges, Node &srcNode,
              Node &dstNode, Node &limitedNodeNum)
{
    cin >> nodeNum >> edgeNum;
    Edge curEdge;
    for (int i = 0; i < edgeNum; i++) {
        cin >> curEdge.fromNode >> curEdge.toNode >> curEdge.edgeWeight;
        edges.push_back(curEdge);
    }
    cin >> srcNode >> dstNode >> limitedNodeNum;
}

void init(vector<int> &minDistance, const int &nodeNum, const Node &srcNode)
{
    minDistance.resize(nodeNum + 1, INT_MAX);
    minDistance[srcNode] = 0;
}

void relaxEdge(const vector<Edge> &edges, vector<int> &minDistance)
{
    vector<int> minDistance_copy = minDistance;
    for (Edge curedge : edges) {
        // 计算原始距离originDistance的时候需要用更新后的minDistance
        // 因为本轮松弛中可能更新了经过其他节点到curedge.toNode的路径
        int originDistance = minDistance[curedge.toNode];
        int newDistance =
            minDistance_copy[curedge.fromNode] + curedge.edgeWeight;

        bool thereIsShorterPath =
            (minDistance_copy[curedge.fromNode] != INT_MAX) &&
            (newDistance < originDistance);

        if (thereIsShorterPath) {
            minDistance[curedge.toNode] = newDistance;
        }
    }
}

void printResult(const vector<int> &minDistance, const Node &dstNode)
{
    if (minDistance[dstNode] == INT_MAX) {
        cout << "unreachable";
    }
    else {
        cout << minDistance[dstNode];
    }
}

void solve()
{
    int nodeNum = 0;
    int edgeNum = 0;
    vector<Edge> edges;
    Node srcNode = 0;
    Node dstNode = 0;
    Node limitedNodeNum = 0;
    vector<int> minDistance;
    getInput(nodeNum, edgeNum, edges, srcNode, dstNode, limitedNodeNum);

    init(minDistance, nodeNum, srcNode);

    int loops = limitedNodeNum + 1;
    while (loops--) {
        relaxEdge(edges, minDistance);
    }

    printResult(minDistance, dstNode);
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```

