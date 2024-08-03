### 二十三、最短路径-dijkstra算法（堆优化）

[代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0047.参会dijkstra堆.html#思路)

[47. 参加科学大会（第六期模拟笔试） (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1047)

#### 1、思路

- 通过小顶堆**维护当前能接触到的节点中，离源节点最近的节点（堆中存节点序号和入边的权值）**，代替原本遍历minDistance的做法；
- 时间复杂度从**遍历数组的O(N)**变为**操作堆的O(logE)**，N为节点数，E为边数；
- 适用于边数较少的**稀疏图**；

#### 2、注意点

- 注意图中的节点结构和小顶堆中的节点结构是不同的，图中存节点序号和入边的长度，小顶堆中存的是节点序号和源节点到当前节点的最短路径长度；

    ```c++
    struct map_NodeAndInEdge {
        Node node;
        int incomingEdgeWeight;
    };
    
    struct heap_NodeAndShortestPath {
        Node node;
        int ShortestPath;
    };
    ```

#### 3、代码

```c++
#include <climits>
#include <iostream>
#include <list>
#include <queue>
#include <vector>
using namespace std;

#define VISITED true
#define UNVISITED false
#define NodeHaveBeenVisited -1

typedef int Node;

struct map_NodeAndInEdge {
    Node node;
    int incomingEdgeWeight;
};

struct heap_NodeAndShortestPath {
    Node node;
    int ShortestPath;
};

struct smallTop {
    bool operator()(const heap_NodeAndShortestPath &a,
                    const heap_NodeAndShortestPath &b)
    {
        return a.ShortestPath > b.ShortestPath;
    }
};

typedef priority_queue<heap_NodeAndShortestPath,
                       vector<heap_NodeAndShortestPath>, smallTop>
    mySmallTopHeap;

void getInput(int &nodeNum, int &edgeNum, vector<list<map_InEdgeAndNode>> &map)
{
    cin >> nodeNum >> edgeNum;
    map.resize(nodeNum + 1);
    int node1 = 0;
    int node2 = 0;
    int edgeWeight = 0;
    map_InEdgeAndNode curNode;
    for (int i = 0; i < edgeNum; i++) {
        cin >> node1 >> node2 >> edgeWeight;
        curNode.node = node2;
        curNode.incomingEdgeWeight = edgeWeight;
        map[node1].push_back(curNode);
    }
}

Node findNearestUnvisitedNode(mySmallTopHeap &heap_ShortestPath,
                              vector<bool> &isVisited)
{
    heap_NodeAndShortestPath curNode = heap_ShortestPath.top();
    heap_ShortestPath.pop();
    if (isVisited[curNode.node] == UNVISITED) {
        return curNode.node;
    }
    return NodeHaveBeenVisited;
}

void putNodeInVisited(Node newAddedNode, vector<bool> &isVisited)
{
    isVisited[newAddedNode] = VISITED;
}

void updateMinDistance(const vector<list<map_InEdgeAndNode>> &map,
                       const Node &newAddedNode, vector<int> &minDistance,
                       mySmallTopHeap &heap_ShortestPath)
{
    heap_NodeAndShortestPath pushNode;
    for (auto i : map[newAddedNode]) {

        bool thereIsCloserPath = (minDistance[newAddedNode] +
                                  i.incomingEdgeWeight) < minDistance[i.node];

        if (thereIsCloserPath) {
            minDistance[i.node] =
                minDistance[newAddedNode] + i.incomingEdgeWeight;

            pushNode.node = i.node;
            pushNode.ShortestPath = minDistance[i.node];
            heap_ShortestPath.push(pushNode);
        }
    }
}

void printResult(const vector<int> &minDistance)
{
    if (minDistance.back() == INT_MAX) {
        cout << -1;
        return;
    }
    cout << minDistance.back();
}

void solve()
{
    int nodeNum = 0;
    int edgeNum = 0;
    vector<list<map_InEdgeAndNode>> map;
    getInput(nodeNum, edgeNum, map);

    vector<int> minDistance(nodeNum + 1, INT_MAX);
    minDistance[1] = 0;

    vector<bool> isVisited(nodeNum + 1, UNVISITED);

    mySmallTopHeap heap_ShortestPath;
    heap_NodeAndShortestPath startNode;
    startNode.node = 1;
    startNode.ShortestPath = 0;
    heap_ShortestPath.push(startNode);

    while (!heap_ShortestPath.empty()) {

        Node NearestUnvisitedNode =
            findNearestUnvisitedNode(heap_ShortestPath, isVisited);

        // 如果从堆中获取的节点已经被访问过了，则跳过它
        if (NearestUnvisitedNode == NodeHaveBeenVisited) {
            continue;
        }

        putNodeInVisited(NearestUnvisitedNode, isVisited);

        updateMinDistance(map, NearestUnvisitedNode, minDistance,
                          heap_ShortestPath);
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

### 二十四、带负权值边的最短路径-Bellman_Ford算法

[94. 城市间货物运输 I (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1152)

[代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0094.城市间货物运输I.html)

#### 1、思想

- 从边入手，对所有边依次做**”松弛“**，进行nodeNum-1次所有边的松弛，就必然能找到源节点到所有节点的最短路径（本题不考虑负权环）；

- **松弛**：观察当前边所指向的目标节点destNode和边的出发点sourceNode，若源节点经过sourceNode到destNode的距离（即minDistance[sourceNode]+edgeWeight） < destNode当前的最短距离（即minDistance[destNode]），则更新destNode的最短距离；

    ```c++
    int curMinDistance = minDistance[i.destNode];
            int newDistance = minDistance[i.sourceNode] + i.edgeWeight;
            // 要判断sourceNode当前的最短路径，如果是MAX_EdgeWeight,就算新距离减小了也不更新
            // （当边的权值为负时，新距离会减小）
            bool thereIsShorterDistance =
                (minDistance[i.sourceNode] != MAX_EdgeWeight) &&
                (newDistance < curMinDistance);
    
            if (thereIsShorterDistance) {
                minDistance[i.destNode] = newDistance;
            }
    ```

- 其实用的是动态规划的思想，将问题分解为子问题（每次从源节点外扩一条边），依次推导；

#### 2、注意点

- 要判断sourceNode当前的最短路径，如果是MAX_EdgeWeight，就算新距离减小了也不更新（当边的权值为负时，新距离会减小）；

#### 3、`代码`

```c++
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

#define MAX_EdgeWeight 101

typedef int Node;

struct Edge {
    Node sourceNode;
    Node destNode;
    int edgeWeight;
};

void getInput(int &nodeNum, int &edgeNum, vector<Edge> &edges)
{
    cin >> nodeNum >> edgeNum;
    Edge curEdge;
    for (int i = 0; i < edgeNum; i++) {
        cin >> curEdge.sourceNode >> curEdge.destNode >> curEdge.edgeWeight;
        edges.push_back(curEdge);
    }
}

void relaxEdge(const vector<Edge> &edges, vector<int> &minDistance)
{
    for (auto i : edges) {
        int curMinDistance = minDistance[i.destNode];
        int newDistance = minDistance[i.sourceNode] + i.edgeWeight;
        // 要判断当前边的出发节点最短路径，如果是MAX_EdgeWeight,就算新距离减小了也不更新
        // （当边的权值为负时，新距离会减小）
        bool thereIsShorterDistance =
            (minDistance[i.sourceNode] != MAX_EdgeWeight) &&
            (newDistance < curMinDistance);

        if (thereIsShorterDistance) {
            minDistance[i.destNode] = newDistance;
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
    vector<Edge> edges;
    getInput(nodeNum, edgeNum, edges);

    vector<int> minDistance(nodeNum + 1, MAX_EdgeWeight);
    minDistance[1] = 0;

    int loops = nodeNum - 1;
    while (loops--) {
        relaxEdge(edges, minDistance);
        // for (auto i : minDistance) {
        //     cout << i << " ";
        // }
        // cout << endl;
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