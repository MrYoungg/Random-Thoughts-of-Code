### 二十八、Floyd算法-多源最短路径

[代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0097.小明逛公园.html#代码如下)

[97. 小明逛公园 (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1155)

#### 1、思路

- 遍历每个节点，判断以当前节点作为中间节点时到达其他节点的最短路径；
- 地图更新可以看成三维数组的逐层遍历；

#### 2、注意点

- 注意题目描述是双向道路，因此是无向图；
- path中存储具体路径；

#### 3、代码

```c++
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

// 尽量不要使用最大整形INT_MAX，容易溢出，发生未定义行为；根据题目具体条件定义最大值即可
// #define Unreachable INT_MAX
#define Unreachable 100000
#define NoPath -1
#define ToItself 0

typedef vector<vector<vector<int>>> AdjacencyMatrix;
struct StartEndPair {
    int startNode;
    int endNode;
};

void print2DVec(const AdjacencyMatrix &vec, int k)
{
    for (int i = 0; i < vec.size(); i++) {
        for (int j = 0; j < vec[0].size(); j++) {
            if (vec[i][j][k] == INT_MAX) {
                cout << "N\t";
            }
            else {
                cout << vec[i][j][k] << '\t';
            }
        }
        cout << endl;
    }
}

void getInput(int &nodeNum, int &edgeNum, AdjacencyMatrix &map,
              AdjacencyMatrix &path, int &pairNum,
              vector<StartEndPair> &NodePairs)
{
    cin >> nodeNum >> edgeNum;
    AdjacencyMatrix tmpMap(
        nodeNum + 1, vector<vector<int>>(
                         nodeNum + 1, vector<int>(nodeNum + 1, Unreachable)));
    AdjacencyMatrix tmpPath(
        nodeNum + 1,
        vector<vector<int>>(nodeNum + 1, vector<int>(nodeNum + 1, NoPath)));

    int fromNode = 0;
    int toNode = 0;
    int edgeWeight = 0;
    for (int i = 0; i < edgeNum; i++) {
        cin >> fromNode >> toNode >> edgeWeight;
        // 注意是双向图
        tmpMap[fromNode][toNode][0] = edgeWeight;
        tmpMap[toNode][fromNode][0] = edgeWeight;
    }

    map = tmpMap;
    path = tmpPath;

    cin >> pairNum;
    StartEndPair curPair;
    for (int i = 0; i < pairNum; i++) {
        cin >> curPair.startNode >> curPair.endNode;
        NodePairs.push_back(curPair);
    }
}

void init(AdjacencyMatrix &map, AdjacencyMatrix &path)
{
    for (int i = 1; i < map.size(); i++) {
        int j = i;
        map[i][j][0] = ToItself;
    }

    for (int i = 1; i < path.size(); i++) {
        for (int j = 1; j < path[0].size(); j++) {
            if (map[i][j][0] != Unreachable)
                path[i][j][0] = i;
        }
    }
}

void updateMapAndPath(int fromNode, int toNode, int throughNode,
                      AdjacencyMatrix &map, AdjacencyMatrix &path)
{

    long long originDistance = map[fromNode][toNode][throughNode - 1];

    // 如果使用INT_MAX,此处运算前应先将2项强转为longlong变量
    // ，否则会以int形式交给newDistance
    long long newDistance =
        (long long)map[fromNode][throughNode][throughNode - 1] +
        (long long)map[throughNode][toNode][throughNode - 1];

    // cout << "fromNode:" << fromNode << endl;
    // cout << "toNode:" << toNode << endl;
    // cout << "throughNode:" << throughNode << endl;
    // cout << "originDistance:" << originDistance << endl;
    // cout << "newDistance:" << newDistance << endl;
    // cout << endl;

    map[fromNode][toNode][throughNode] = min(originDistance, newDistance);

    if (newDistance < originDistance) {

        path[fromNode][toNode][throughNode] =
            path[throughNode][toNode][throughNode - 1];
    }
    else {
        path[fromNode][toNode][throughNode] =
            path[fromNode][toNode][throughNode - 1];
    }
}

void printResult(const AdjacencyMatrix &map,
                 const vector<StartEndPair> &NodePairs)
{
    for (int i = 0; i < NodePairs.size(); i++) {
        int startNode = NodePairs[i].startNode;
        int endNode = NodePairs[i].endNode;

        if (map[startNode][endNode].back() == Unreachable) {
            cout << -1 << endl;
        }
        else {
            cout << map[startNode][endNode].back() << endl;
        }
    }
}

void solve()
{
    int nodeNum = 0;
    int edgeNum = 0;
    AdjacencyMatrix map;
    AdjacencyMatrix path;
    int pairNum = 0;
    vector<StartEndPair> NodePairs;
    getInput(nodeNum, edgeNum, map, path, pairNum, NodePairs);

    init(map, path);
    // print2DVec(map, 0);
    // print2DVec(path, 0);

    for (int k = 1; k <= nodeNum; k++) {
        for (int i = 1; i <= nodeNum; i++) {
            for (int j = 1; j <= nodeNum; j++) {
                updateMapAndPath(i, j, k, map, path);
            }
        }
        // cout << "k:" << k << endl;
        // cout << "map:" << endl;
        // print2DVec(map, k);
        // cout << "path:" << endl;
        // print2DVec(path, k);
        // cout << endl;
    }

    printResult(map, NodePairs);
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```

### 二十九、Astar算法-趋向目标方向的最短路径

[127. 骑士的攻击 (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1203)

[A * 算法精讲 （A star算法） | 代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0126.骑士的攻击astar.html#astar)

#### 1、思路

- 启发式函数将搜索方向引导到目标方向；

#### 2、注意点

#### 3、代码

```c++
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

#define directionNum 8
#define MapLength 5
#define MapWidth 5

vector<int> minDistanceResult;

int dirOffset[8][2] = {{-2, -1}, {-2, 1}, {-1, -2}, {-1, 2},
                       {1, -2},  {1, 2},  {2, -1},  {2, 1}};

struct Node {
    int x;
    int y;
    int weight;
    bool operator<(const Node &curNode) const
    {
        return weight > curNode.weight; // 定义小顶堆
    }
};

struct PathPlan {
    Node startNode;
    Node endNode;
};

void getInput(int &planNum, vector<PathPlan> &plans)
{
    cin >> planNum;
    PathPlan curPlan;
    for (int i = 0; i < planNum; i++) {
        cin >> curPlan.startNode.x >> curPlan.startNode.y;
        cin >> curPlan.endNode.x >> curPlan.endNode.y;
        plans.push_back(curPlan);
    }
}

int getWeight(const Node node1, const Node node2)
{
    return (node1.x - node2.x) * (node1.x - node2.x) +
           (node1.y - node2.y) * (node1.y - node2.y);
}

void handleSurroundingNode(const Node &curNode, const Node &endNode,
                           vector<vector<int>> &minDistance,
                           priority_queue<Node> &nodeQueue)
{
    for (int i = 0; i < directionNum; i++) {
        Node newNode;
        newNode.x = curNode.x + dirOffset[i][0];
        newNode.y = curNode.y + dirOffset[i][1];
        cout << "newnode:" << '(' << newNode.x << ',' << newNode.y << ')';

        bool nodeOverMap = (newNode.x < 1 || newNode.x > MapLength) ||
                           (newNode.y < 1 || newNode.y > MapWidth);
        if (nodeOverMap) {
            cout << " x" << endl;
            continue;
        }

        bool nodeHaveBeenVisited = minDistance[newNode.x][newNode.y] != 0;
        if (nodeHaveBeenVisited) {
            cout << "have been visited" << endl;
            continue;
        }

        newNode.weight = getWeight(newNode, endNode);
        cout << " weight:" << newNode.weight;

        minDistance[newNode.x][newNode.y] =
            minDistance[curNode.x][curNode.y] + 1;
        cout << " mindistance = " << minDistance[curNode.x][curNode.y]
             << "+1 = " << minDistance[newNode.x][newNode.y] << endl;

        newNode.weight = getWeight(newNode, endNode);
        nodeQueue.push(newNode);
    }
}

void findMinDistance_Astar(Node &startNode, const Node &endNode)
{
    vector<vector<int>> minDistance(MapLength + 1,
                                    vector<int>(MapWidth + 1, 0));

    priority_queue<Node> nodeQueue;
    startNode.weight = getWeight(startNode, endNode);
    nodeQueue.push(startNode);

    Node curNode;
    while (!nodeQueue.empty()) {
        curNode = nodeQueue.top();
        nodeQueue.pop();
        cout << "curnode:" << '(' << curNode.x << ',' << curNode.y << ')'
             << endl;

        bool reachEndNode =
            (curNode.x == endNode.x) && (curNode.y == endNode.y);
        if (reachEndNode) {
            cout << "reach end node" << endl;
            minDistanceResult.push_back(minDistance[endNode.x][endNode.y]);
            return;
        }

        handleSurroundingNode(curNode, endNode, minDistance, nodeQueue);
    }
}

void printResult() {}

void solve()
{
    int planNum = 0;
    vector<PathPlan> plans;

    getInput(planNum, plans);

    for (auto curPlan : plans) {

        findMinDistance_Astar(curPlan.startNode, curPlan.endNode);
    }

    printResult();
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```

# 