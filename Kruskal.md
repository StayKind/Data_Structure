Kruskal 算法
<!--more-->
### 简单介绍最小生成树 ###
给定一个无向图，如果它任意两个顶点都联通并且是一棵树，那么我们就称之为生成树(Spanning Tree)。如果是带权值的无向图，那么权值之和最小的生成树，我们就称之为最小生成树(MST, Minimum Spanning Tree)。
我们由最小生成树的定义，可以延伸出一个修建道路的问题：把无向图的每个顶点看作村庄，计划修建道路使得可以在所有村庄之间通行。把每个村庄之间修建道路的费用看作权值，那么我们就可以得到一个求解修建道路的最小费用的问题。
### 算法描述： ###
Kruskal算法是基于贪心的思想得到的。首先我们把所有的边按照权值先从小到大排列，接着按照顺序选取n-1条边，如果选取的边构不成回路，那么就将它们合并。取够n-1条边为止。
<br>**下图显示Kruskal的算法过程：**

![](http://115.159.67.147/wp-content/uploads/2017/07/kruskal-1.jpg)
### 伪代码: ###

算法的伪代码如下，其中A保存最小生成树，MAKE-SET(v)表示构造一棵只有顶点v的树，FIND-SET(u)表示找u所在树的根节点，Union(u, v)表示合并u和v的所在树：
```cpp
　　　　MST-KRUSKAL(G, W)

　　　　　　A←∅

　　　　　　for each vertex v∈V[G]

　　　　　　　　do MAKE-SET(v)

　　　　　　sort the eages of E into nondecreasing order by weight w

　　　　　　for each eage(u, v)∈E, teken in nondecreasing order by weight

　　　　　　　　do if(FIND-SET(u) ≠ FIND-SET(v))

　　　　　　　　　　then A←A∪{ (u, v) }

　　　　　　　　　　　　Union(u, v)

　　　　return A

```
### 代码部分： ###

```cpp
//n为边的数量，m为点的数量
int Kruskal(int n, int m)
{
    int nEdge = 0, res = 0;  //nEdge  表示如果加入集合的点
    sort(a, a+n, cmp);//将边按照权值从小到大排序
    for(int i = 0; i < n && nEdge != m - 1; i++)
    {
        //判断当前这条边的两个端点是否属于同一棵树
        if(find(a[i].a) != find(a[i].b))
        {
            //如果不属于同一棵树，就要合并
            unite(a[i].a, a[i].b);
            res += a[i].price;  //最小生成树的值加上a[i].price
            nEdge++;
        }
    }
    //如果加入边的数量小于m - 1,则表明该无向图不连通,等价于不存在最小生成树
    if(nEdge < m-1) res = -1;
    return res;
}
```

### 实战演练： ###
我们现在已经基本了解了Kruskal算法，让我们来一道题目练练手：[畅通工程](http://acm.hdu.edu.cn/showproblem.php?pid=1863)。这是一道非常基本的最小生成树的应用，所以我就不做详细说明了，这里仅附上代码以供参考：
```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cstring>
#define MAXN 10000 + 10
using namespace std;

int par[MAXN], Rank[MAXN];
typedef struct
{
    int a, b, price;
} Node;
Node a[MAXN];

int cmp(Node a, Node b)
{
    return a.price < b.price;
}
void Init(int n)
{
    for(int i = 0; i < n; i++)
    {
        Rank[i] = 0;
        par[i] = i;
    }
}

int find(int x)
{
    int root = x;
    while(root != par[root]) root = par[root];
    while(x != root)
    {
        int t = par[x];
        par[x] = root;
        x = t;
    }
    return root;
}

void unite(int x, int y)
{
    x = find(x);
    y = find(y);
    if(Rank[x] < Rank[y])
    {
        par[x] = y;
    }
    else
    {
        par[y] = x;
        if(Rank[x] == Rank[y]) Rank[x]++;
    }
}
//n为边的数量，m为点的数量
int Kruskal(int n, int m)
{
    int nEdge = 0, res = 0;  //nEdge  表示如果加入集合的点
    sort(a, a+n, cmp);//将边按照权值从小到大排序
    for(int i = 0; i < n && nEdge != m - 1; i++)
    {
        //判断当前这条边的两个端点是否属于同一棵树
        if(find(a[i].a) != find(a[i].b))
        {
            //如果不属于同一棵树，就要合并
            unite(a[i].a, a[i].b);
            res += a[i].price;  //最小生成树的值加上a[i].price
            nEdge++;
        }
    }
    //如果加入边的数量小于m - 1,则表明该无向图不连通,等价于不存在最小生成树
    if(nEdge < m-1) res = -1;
    return res;
}
int main()
{
    int n, m, ans;
    while(scanf("%d%d", &n, &m), n)
    {
        Init(m);
        for(int i = 0; i < n; i++)
        {
            scanf("%d%d%d", &a[i].a, &a[i].b, &a[i].price);
            //将村庄编号变为0~m-1（这个仅仅只是个人习惯，并非必要的）
            a[i].a--;
            a[i].b--;
        }
        ans = Kruskal(n, m);
        if(ans == -1)
            printf("?\n");
        else
            printf("%d\n", ans);
    }
    return 0;
}
```

[toc]