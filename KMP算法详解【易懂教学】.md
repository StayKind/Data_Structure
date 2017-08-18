KMP算法详解【易懂教学】
### 引入篇 ###
KMP算法又称“看毛片”算法[/捂脸]，是一个效率非常高的字符串匹配算法；是一种在线性时间内能处理两个字符串的包含关系的算法。那有没有效率更高的呢？当然是有的，就是Boyer-Moore算法。这里就只讲述一下KMP。
举例说明该算法的**核心**：求一个字符串里有没有另一个字符串返回其下标；一个字符串里有几个另一个字符串。
问A中是否存在B：

>A="abcaabababaa"
>B="abab"

常规的方法就是暴力进行比较，如下图：

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP_1.gif)

但这样的是非常耗时的，时间复杂度达到了O(n*m)。KMP算法的复杂的可以优化到O(n+m)
我们既然已经知道了在这一步骤中不匹配

![](http://www.adreame.com/wp-content/uploads/2017/08/o_图片4.png)

但知道向后移一步是不成立的。a和bc都不会匹配，还要继续向后移动。所以我们就需要对这一点进行进一步的优化，让我们的算法可以把根本不匹配的部分直接跳过去。这就是下面要讲的KMP算法。

### 算法篇 ###

- 首先需要知道**前缀和后缀的概念**
前缀：指的是字符串的子串中从原串最前面开始的子串，不会加入最后的字符，如abab的前缀有：a,ab,aba
后缀：指的是字符串的子串中在原串结尾处结尾的子串，不会加入第一个字符，如abab的后缀有：b,ab,bab
- 接着是next数组：next[i]的值就是i下标之前的字符串的最长公共前缀和后缀的长度。如abab的next[4]，看上面前缀后缀的部分可以知道，公共的前缀后缀为ab，则next[4]=2。
- 最后就是通过next数组进行KMP匹配算法。

#### next数组篇 ####
下面将以一个典型的例子来介绍next数组。

>A="abaabaabbabaaabaabbabaab"
>B="abaabbabaab"

其next数组的值为：

![](http://www.adreame.com/wp-content/uploads/2017/08/next-.png)

**注意：**next数组下标从1开始，0的位置为-1，目的是方便判越界。

#### next数组代码 ####
```cpp
void GetNext(string B)
{
    int len_B = B.size();
    nex[0] = -1;
    int k = -1,j = 0 ;
    while(j < len_B)
    {
        if(k == -1 || B[j] == B[k])///k可以理解为公共前缀后缀的长度，同时也算下标的指针
        {
            ++ k; ++ j;
            nex[j] = k;
        }
        else
            k = nex[k];
    }
}
```

代码部分较为难懂，可以慢慢理解。先知道他什么意思就可以了。

----

#### KMP函数 ####
还是这个例子：

>A="abaabaabbabaaabaabbabaab"
>B="abaabbabaab"

- 首先我们先规定i为A数组的下标，j为B数组的下标。i，j两指针同时向前匹配。为了方便，**我们都认为字符串从1开始匹配，在实际的算法当中我们都是从0开始的**。

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP2-2.gif)

- 可以在i = 6,j = 6的位置出现不匹配，这时候找nex[j-1]的位置，值为2，直接可以将B数组指针j拉到nex[j-1]+1的位置。这时候i = 6,j = 3。

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP3.gif)

- 好下面就接着向后面匹配

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP4.gif)

- 这时候在i = 14,j = 11的位置出现不匹配，同上，找到nex[j-1]值为4，j = nex[j-1]+1。这时候i = 14,j = 5。

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP5.gif)

- 接着匹配

![](http://www.adreame.com/wp-content/uploads/2017/08/o_图片28.png)

- i = 14,j = 5 出现不匹配，j 变成 2接着向后匹配

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP6.gif)

- 接着就是又撞南墙

![](http://www.adreame.com/wp-content/uploads/2017/08/o_图片33.png)

- i = 14，j变成1开始匹配。

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP8.gif)

最后配对成功。

**附上完整的KMP匹配过程：**

![](http://www.adreame.com/wp-content/uploads/2017/08/o_KMP9.gif)

> 郑重声明一下，图片从[**SYCstudio**](http://www.cnblogs.com/SYCstudio/p/7194315.html)转载来的，特别感谢。

#### KMP函数代码 ####
```cpp
int kmp(string A, string B)
{
    int ans = -1, i = 0, j = 0;
    int len_B = B.size(), n = A.size();
    while(i<n)
    {
        if(j==-1||A[i] == B[j]) ///如果匹配向后移
        {
            ++i;
            ++j;
        }
        else ///不匹配就可以通过nex数组跳到合适的位置
            j = nex[j];
        if(j == len_B)
        {
            return i-len_B + 1; //A数组从0开始所以应返回的位置+1.
        }
    }
    return ans;
}
```

---

### KMP应用 ###
字符串匹配问题。学习AC自动机也需要掌握的算法。等等……
#### 练习题： ####
[POJ_1961](http://poj.org/problem?id=1961)
[POJ_3461](http://poj.org/problem?id=3461)
[POJ_2752](http://poj.org/problem?id=2752)
[POJ_2406](http://poj.org/problem?id=2406)
[HDU_2087](http://acm.hdu.edu.cn/showproblem.php?pid=2087)
[HDU_1686](http://acm.hdu.edu.cn/showproblem.php?pid=1686)
[HDU_2203](http://acm.hdu.edu.cn/showproblem.php?pid=2203)
[HDU_1358](http://acm.hdu.edu.cn/showproblem.php?pid=1358)
[HDU_1711](http://acm.hdu.edu.cn/showproblem.php?pid=1711)
[HDU_1238](http://acm.hdu.edu.cn/showproblem.php?pid=1238)
### 参考博客 ###
>[**温姑娘的博客**](http://blog.csdn.net/wyxeainn/article/details/77148159#reply)
>[**SYCstudio**](http://www.cnblogs.com/SYCstudio/p/7194315.html)