字典树详解和应用【Trie模板】
### Trie的简介 ###
Trie树，又称单词查找树或键树，是一种树形结构，是一种哈希树的变种。典型应用是用于统计和排序大量的字符串（但不仅限于字符串），所以经常被搜索引擎系统用于文本词频统计。它的优点是：最大限度地减少无谓的字符串比较，查询效率比哈希表高。
**Trie的核心思想是空间换时间。利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。**
#### Trie树的特性 ####

- 根节点不包含字符，除根节点外每一个节点都只包含一个字符。
- 从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串。
- 每个节点的所有子节点包含的字符都不相同。
- 如果字符的种数为n，则每个结点的出度为n，这也是空间换时间的体现，浪费了很多的空间。
- 插入查找的复杂度为O(n)，n为字符串长度。

#### Trie的应用 ####
**串的快速检索 **
检索/查询功能是Trie树最原始的功能。思路就是从根节点开始一个一个字符进行比较： 如果沿路比较，发现不同的字符，则表示该字符串在集合中不存在。如果所有的字符全部比较完并且全部相同，还需判断最后一个节点的标志位（exist是否为true）。
**词频统计**
Trie树常被搜索引擎系统用于文本词频统计 。
为了实现词频统计，我们修改了节点结构，用一个整型变量cnt来计数对每一个关键字执行插入操作，若已存在，计数加1，若不存在，插入后cnt为1。
**“串”排序 **
Trie树可以对大量字符串按字典序进行排序，思路也很简单：遍历一次所有关键字，将它们全部插入trie树，树的每个结点的所有儿子很显然地按照字母表排序，然后先序遍历输出Trie树中所有关键字即可。
**前缀匹配 **
trie树前缀匹配常用于搜索提示。如当输入一个网址，可以自动搜索出可能的选择。当没有完全匹配的搜索结果，可以返回前缀最相似的可能。
找出一个字符串集合中所有以ab开头的字符串。我们只需要用所有字符串构造一个trie树，然后输出以a->b->开头的路径上的关键字即可。
**作为其他数据结构和算法的辅助结构**
如后缀树，AC自动机
**等等……**
### Trie的数据结构 ###
利用串构建一个字典树，这个字典树保存了串的公共前缀信息，因此可以降低查询操作的复杂度。
下面以英文单词构建的字典树为例，这棵Trie树中每个结点包括26个孩子结点，因为总共有26个英文字母(假设单词都是小写字母组成)。
则可声明包含Trie树的结点信息的结构体:

```cpp
const int en = 26;
const int maxn = 1e6;
struct Trie
{
    int cnt;         //记录节点代表的单词的个数
    Trie *nex[en];    //26个字母的各个子节点
}T[maxn];//字典树的静态结点数组
```

![](http://www.adreame.com/wp-content/uploads/2017/08/Trie.png)
上图是一棵Trie树，表示了关键字集合{“a”, “to”, “tea”, “ted”, “ten”, “i”, “in”, “inn”} 。exist为true的为蓝色的标记，且cnt数量都为1

Trie树的根结点不包含任何信息，由图可见根节点的nex数组下表t,a,i不为NULL。
### Trie的模板练习 ###

> [题目链接](http://acm.sdut.edu.cn/onlinejudge2/index.php/Home/Index/problemdetail/pid/2828.html)

#### 题目描述 ####
输入n个单词，接着输入m个单词问这单词是否在那n个单词中出现。
#### 解题思路 ####
字典树模板题,注意，gcc 无法使用c++头文件`#include <bits/stdc++.h>`，改成gcc就过了。
#### 代码部分 ####
```cpp
#include <bits/stdc++.h>
using namespace std;
const int en = 26;
const int maxn = 1e6;
struct Trie
{
    int cnt;         //记录节点代表的单词的个数
    Trie *nex[en];    //26个字母的各个子节点
}T[maxn];//字典树的静态结点数组
int top; //标记下标
inline int idx(char c) {return c - 'a';}
inline Trie * CreatTrie()
{
    Trie *p = &T[top ++];
    p -> cnt = 0;
    for(int i = 0; i< en; ++ i) p -> nex[i] = NULL;
    return p;
}
inline Trie * Init() {top = 0; return CreatTrie();}
void Insert(Trie *root, string s)
{
    Trie *p = root;
    for(int i = 0; i < s.size(); ++ i)
    {
        int temp = idx(s[i]);
        if(p -> nex[temp] == NULL) p -> nex[temp] = CreatTrie();
        p = p -> nex[temp];
    }
    p -> cnt ++;
}
int Search(Trie *root, string s)
{
    Trie *p = root;
    for(int i = 0; i < s.size(); ++ i)
    {
        int temp = idx(s[i]);
        if(p -> nex[temp] == NULL) return 0;
        p = p -> nex[temp];
    }
    return p -> cnt;
}
int main()
{
    int n, m;
    string s;
    while(cin >> n >> m && n && m)
    {
        Trie *root = Init();
        for(int i = 1; i <= n; ++ i) cin >> s, Insert(root, s);
        for(int i = 1; i <= m; ++ i) cin >> s, puts(Search(root, s) ? "Yes" : "No");
    }
    return 0;
}

```