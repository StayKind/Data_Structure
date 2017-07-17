SPFA算法
# 算法背景 #
>## SPFA（Shortest Path Faster Algorithm）算法，是西南交通大学段凡丁于 1994 年发表的，其在 Bellman-ford 算法的基础上加上一个队列优化，减少了冗余的松弛操作，是一种高效的最短路算法。 ##

# 算法思想 #
>## 设立一个队列用来保存待优化的顶点，优化时每次取出队首顶点 u，并且用 u 点当前的最短路径估计值dist[u]对与 u 点邻接的顶点 v 进行松弛操作，如果 v 点的最短路径估计值dist[v]可以更小，且 v 点不在当前的队列中，就将 v 点放入队尾。这样不断从队列中取出顶点来进行松弛操作，直至队列空为止。（所谓的松弛操作，简单来说，对于顶点 i，把dist[i]调整更小或更大。更多解释请参考百科：松弛操作) ##

>## 而其检测负权回路的方法也很简单，如果某个点进入队列的次数大于等于 n，则存在负权回路，其中 n 为图的顶点数。 ##

# 代码实现 #
```cpp
#include<iostream>    
#include<queue>
#include<stack>
#include<string.h>
using namespace std;

int matrix[100][100];  //邻接矩阵
bool visited[100];     //标记数组
int dist[100];         //源点到顶点i的最短距离
int path[100];         //记录最短路的路径
int enqueue_num[100];  //记录入队次数
int vertex_num;        //顶点数
int edge_num;          //边数
int source;            //源点

bool SPFA()
{
    memset(visited, 0, sizeof(visited));
    memset(enqueue_num, 0, sizeof(enqueue_num));
    for (int i = 0; i < vertex_num; i++)
    {
        dist[i] = INT_MAX;
        path[i] = source;
    }

    queue<int> Q;
    Q.push(source);
    dist[source] = 0;
    visited[source] = 1;
    enqueue_num[source]++;
    while (!Q.empty())
    {
        int u = Q.front();
        Q.pop();
        visited[u] = 0;
        for (int v = 0; v < vertex_num; v++)
        {
            if (matrix[u][v] != INT_MAX)  //u与v直接邻接
            {
                if (dist[u] + matrix[u][v] < dist[v])
                {
                    dist[v] = dist[u] + matrix[u][v];
                    path[v] = u;
                    if (!visited[v])
                    {
                        Q.push(v);
                        enqueue_num[v]++;
                        if (enqueue_num[v] >= vertex_num)
                            return false;
                        visited[v] = 1;
                    }
                }
            }
        }
    }
    return true;
}

void Print()
{
    for (int i = 0; i < vertex_num; i++)
    {
        if (i != source)
        {
            int p = i;
            stack<int> s;
            cout << "顶点 " << source << " 到顶点 " << p << " 的最短路径是： ";

            while (source != p)  //路径顺序是逆向的，所以先保存到栈
            {
                s.push(p);
                p = path[p];
            }

            cout << source;
            while (!s.empty())  //依次从栈中取出的才是正序路径
            {
                cout << "--" << s.top();
                s.pop();
            }
            cout << "    最短路径长度是：" << dist[i] << endl;
        }
    }
}

int main()
{

    cout << "请输入图的顶点数，边数，源点：";
    cin >> vertex_num >> edge_num >> source;

    for (int i = 0; i < vertex_num; i++)
        for (int j = 0; j < vertex_num; j++)
            matrix[i][j] = INT_MAX;  //初始化matrix数组

    cout << "请输入" << edge_num << "条边的信息：\n";
    int u, v, w;
    for (int i = 0; i < edge_num; i++)
    {
        cin >> u >> v >> w;
        matrix[u][v] = w;
    }

    if (SPFA())
        Print();
    else
        cout << "Sorry,it have negative circle!\n";

    return 0;
}

```
