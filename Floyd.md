Floyd 算法
# 简单介绍： #
全称Floyd-Warshall。算法用于求所有点对的最短距离。比调用n次dijkstra的优点在于代码简单。
时间复杂度为O(n^3)
# 原理过程： #
这是一个dp（动态规划的过程）
dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]);
即从顶点i到j且经过顶点k的最短路径长度。
# 代码实现： #
```cpp
void floyd()  
{  
    for(int k=0;k<n;k++)  
        for(int i=0;i<n;i++)  
            for(int j=0;j<n;j++)  
                dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]);  
}
	
```