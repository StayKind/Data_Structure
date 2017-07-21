<!--more-->
STL—deque双向队列
### 简单介绍： ###
deque双向队列是一种双向开口的连续性空间，可以高效的在头尾两段插入和删除元素
### 代码讲解用法： ###
```cpp
#include <iostream>

#include <deque>
using namespace std;

int main()
{
    deque<int> ideq;//声明一个int 类型的双向队列
    deque<int>::iterator pos;//双向队列的迭代器
    ideq.push_back(100);//在尾部插入元素
    ideq.push_front(10);//在头部插入元素

    pos=ideq.begin()+2;
    ideq.insert(pos,5);//在pos位置插入一个元素（5）
    ideq.erase(pos);//删除pos位置的元素

    int t1=ideq.front();//返回第一个数据
    int t2=ideq.back();//返回最后一个数据

    if(ideq.empty())//判断容器是否为空

    int n=ideq.size();//返回容器中的数据个数

    for (pos = ideq.begin(); pos != ideq.end(); pos++)//输出*pos位置的数据
        printf("%d ", *pos);
    return 0;
}

```