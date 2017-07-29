<!--more-->

nth_element使用时需要加algorithm头文件，作用为求第n大的元素，并把它放在第n位置上，可以保证n位置之前的数都比第n位置上的数小。n位置之后的数都比n位置上的数大。但是n位置前后并不是有序的。

Rearranges the elements in the range [first,last),in such a way that the element at the nth position is the element that would be in that position in a sorted sequence. 
### 代码演示： ###
```cpp
#include<iostream>
#include<algorithm>
using namespace std;
int main()
{
    int a[]={1,3,4,5,2,6,8,7,9};
    int i;
    cout<<"数列如下："<<endl;
    for(i=0;i<9;i++)
       cout<<a[i]<<" ";
       cout<<endl;
	//void nth_element (RandomAccessIterator first, RandomAccessIterator nth,RandomAccessIterator last);
    nth_element(a,a+5,a+9);
	
    cout<<endl<<"输出第五大的数： "<<a[4]<<endl; //注意下标是从0开始计数的
    return 0;
}

```

### 用法小结： ###
nth_element(l,k,r)
其中区间为左闭右开：[l,r)
k是实的
O(n)把第k小放到第k位，第k位之前都比它小，第k位之后都比它大
没有排序效果
