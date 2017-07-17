# Dijkstra 邻接矩阵 #

## 前言 ##
最短路径问题，当权值为1或者是无权的情况可以看用BFS来解决，这里只讨论加权的情况。
## 算法的背景： ##
Dijkstra 算法（中文名：迪杰斯特拉算法）是由荷兰计算机科学家 Edsger Wybe Dijkstra 提出。该算法常用于路由算法或者作为其他图算法的一个子模块。举例来说，如果图中的顶点表示城市，而边上的权重表示城市间开车行经的距离，该算法可以用来找到两个城市之间的最短路径。
## 简单描述： ##
Dijkstra单源最短路算法，即计算从起点出发到每个点的最短路。Dijkstra常常作为其他算法的预处理。
使用邻接矩阵的时间复杂度为O(n^2)
用邻接表+优先队列（堆）的时间复杂度为O((m+n)logn)近似为O(mlogn)
## 算法的思想以及过程 ##
一句话来讲就是：每次选择已经访问过（标记为true）的最短边的点，并用这个点进行更新，更新的内容为未访问的点到源点的最短路径。（更新的过程：当该点到源点的距离加上到未访问的点的距离小于未访问过点到源点的距离的时候，对未访问过的点的距离进行更新）。
我们用一个例子来具体说明迪杰斯特拉算法的流程。
![](http://oi0fekpsr.bkt.clouddn.com/%E5%8D%95%E6%BA%90%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84__1.png#mirages-width=450&mirages-height=380&mirages-cdn-type=1)

定义源点为0，`dist[i]`为源点0到顶点i的最短路径。其过程描述如下：

|  步骤  | dist[1] | dist[2] | dist[3] | dist[4] |     已找到的集合     |
| :--:  | :-----: | :-----: | :-----: | :-----: | :------------: |
| 第1步  |    8    |    1    |    2    |   +∞    |     { 2 }      |
| 第2步  |    8    |    ×    |    2    |    4    |    { 2, 3 }    |
| 第3步  |    5    |    ×    |    ×    |    4    |  { 2, 3, 4 }   |
| 第4步  |    5    |    ×    |    ×    |    ×    | { 2, 3, 4, 1 } |
| 第5步  |    ×    |    ×    |    ×    |    ×    | { 2, 3, 4, 1 } |

第1步：从源点0开始，找到与其邻接的点：1，2，3，更新`dist[]`数组，因0不与4邻接，故`dist[4]`为正无穷。在`dist[]`中找到最小值，其顶点为2，即此时已找到0到2的最短路。

第2步：从2开始，继续更新`dist[]`数组：2与1不邻接，不更新；2与3邻接，因`0→2→3`比`dist[3]`大，故不更新`dist[3]` ；2与4邻接，因`0→2→4`比`dist[4]`小，故更新`dist[4]`为4。在`dist[]`中找到最小值，其顶点为3，即此时又找到0到3的最短路。

第3步：从3开始，继续更新`dist[]`数组：3与1邻接，因`0→3→1`比`dist[1]`小，更新`dist[1]`为5；3与4邻接，因`0→3→4`比`dist[4]`大，故不更新。在`dist[]`中找到最小值，其顶点为4，即此时又找到0到4的最短路。

第4步：从4开始，继续更新`dist[]`数组：4与1不邻接，不更新。在`dist[]`中找到最小值，其顶点为1，即此时又找到0到1的最短路。

第5步：所有点都已找到，停止。

对于上述步骤，你可能存在以下的疑问：

![](http://oi0fekpsr.bkt.clouddn.com/%E5%8D%95%E6%BA%90%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84_3.png#mirages-width=460&mirages-height=360&mirages-cdn-type=1)

若A作为源点，与其邻接的只有B，C，D三点，其`dist[]`最小时顶点为C，即就可以确定`A→C`为A到C的最短路。但是我们存在疑问的是：**是否还存在另一条路径使A到C的距离更小？** 用反证法证明。

假设存在如上图的红色虚线路径，使`A→D→C`的距离更小，那么`A→D`作为`A→D→C`的子路径，其距离也比`A→C`小，这与前面所述“`dist[]`最小时顶点为C”矛盾，故假设不成立。因此这个疑问不存在。

根据上面的证明，我们可以推断出，**Dijkstra每次循环都可以确定一个顶点的最短路径，故程序需要循环n-1次。**
## 代码实现 ##
```cpp
#include<iostream>
#include <string.h>
using namespace std;

int matrix[100][100];  //邻接矩阵
bool visited[100];     //标记数组
int dist[100];         //源点到顶点i的最短距离
int path[100];         //记录最短路的路径
int source;            //源点
int vertex_num;        //顶点数
int arc_num;           //弧数

void Dijkstra(int source)
{
    memset(visited, 0, sizeof(visited));  //初始化标记数组
    visited[source] = true;
    for (int i = 0; i < vertex_num; i++)
    {
        dist[i] = matrix[source][i];
        path[i] = source;
    }

    int min_cost;        //权值最小
    int min_cost_index;  //权值最小的下标
    for (int i = 1; i < vertex_num; i++)  //找到源点到另外vertex_num-1个点的最短路径
    {
        min_cost = INT_MAX;
        for (int j = 0; j < vertex_num; j++)
        {
            if (visited[j] == false && dist[j] < min_cost)  //找到权值最小
            {
                min_cost = dist[j];
                min_cost_index = j;
            }
        }

        visited[min_cost_index] = true;  //该点已找到，进行标记

        for (int j = 0; j < vertex_num; j++)  //更新dist数组
        {
            if (visited[j] == false &&
                matrix[min_cost_index][j] != INT_MAX &&  //确保两点之间有弧
                matrix[min_cost_index][j] + min_cost < dist[j])
            {
                dist[j] = matrix[min_cost_index][j] + min_cost;
                path[j] = min_cost_index;
            }
        }
    }
}

int main()
{
    cout << "请输入图的顶点数（<100）：";
    cin >> vertex_num;
    cout << "请输入图的弧数：";
    cin >> arc_num;

    for (int i = 0; i < vertex_num; i++)
        for (int j = 0; j < vertex_num; j++)
            matrix[i][j] = INT_MAX;  //初始化matrix数组

    cout << "请输入弧的信息：\n";
    int u, v, w;
    for (int i = 0; i < arc_num; i++)
    {
        cin >> u >> v >> w;
        matrix[u][v] = matrix[v][u] = w;
    }

    cout << "请输入源点（<" << vertex_num << "）：";
    cin >> source;
    Dijkstra(source);

    for (int i = 0; i < vertex_num; i++)
    {
        if (i != source)
        {
            cout << source << "到" << i << "最短距离是：" << dist[i] << "，路径是：" << i;
            int t = path[i];
            while (t != source)
            {
                cout << "--" << t;
                t = path[t];
            }
            cout << "--" << source << endl;
        }
    }

    return 0;
}
```