## AC自动机

Aho-Corasickautomation，该算法在1975年产生于贝尔实验室，是著名的多模匹配算法。

要学会AC自动机，我们必须知道字典树，也就是Trie树，又称单词查找树或键树，是一种树形结构，是哈希树的变种。

AC自动机一个常见的例子就是给出n个单词，再给出一段包含m个字符的文章，让你找出有多少个单词在文章里出现过。要搞懂AC自动机，先得有字典树Trie和KMP模式匹配算法的基础知识。KMP算法是单模式串的字符匹配算法，AC自动机是多模式串的字符匹配算法。

### **字典树Trie： **###

字典树是一种树形结构。典型的应用是用于统计，排序和保存大量的字符串（但不仅限于字符串），所以经常被搜索引擎系统用于文本词频统计。

它的优点是：利用字符串的公共前缀来减少查询时间，最大限度地减少无谓字符串的比较，查询频率比哈希树高。

总的来说：字典树就像我们平时用的字典一样，我们把所有的单词编排入一个字典里面，当我们查找的时候，首先看该单词的首字母，进入首字母所在的树枝，然后再看第二个字母，在进入相应的树枝，依次类推，直到找到该单词。

### **AC自动机的构造** ###

1、构造一棵Trie树，作为AC自动机的搜索数据结构

2、构造fail指针，使当前字符失配时跳转到具有最长公共前后缀的字符继续匹配。如同KMP算法一样，AC自动机在匹配时如果当前字符匹配失败，那么利用fail指针进行跳转。由此可知如果跳转，跳转后的串的前缀，必定为跳转前的模式串的后缀并且跳转的新位置的深度（匹配字符个数）一定小于跳之前的节点。所以我们利用bfs在Trie上进行fail指针的求解。

3、扫描主串进行匹配。

### **AC自动机详解** ###

现在给出5个单词，say,she,her,he,shr。给定字符串为yasherhs。问多少个单词在其中出现过。

**构造Trie树**

首先我们构造一棵Trie树。这棵树包含三个指针，分别为p,p->fail,temp。

1、指针p，指向当前匹配的字符。若p指向roota，表示当前匹配的字符序列为空。

2、指针p->fail，p的失败指针，指向与p相同的节点，若没有，则指向root。

3、指针temp，其实它就是在建立fail指针时有寻找与p字符匹配的节点的作用，在扫描时作用最大。

**节点的fail指针**

假设当前节点为father，它已经求过fail指针了，如果father出队,我们现在就要求father节点孩子们的失败指针。对于那些非空的孩子节点，由于他们存在，所以我们要求他们的fail指针，对于father下面为空的孩子节点，我们不予理睬就
可以了。

对于father节点的非空孩子t,t必然代表了一个字母，此时我们首先找到father->fail。然后看看father->fail->child  有没有等于和t表示的字符是一样的，如果有的话t->fail = father->fail->child。如果发现没有的话，我们继续往前翻找，father->fail->fail->child有没有和t表示的字符一样的孩子，有的话t->fail = father->fail->fail->child。否则的话继续往前翻找，如果都翻到根了，还没找到对应的节点的话，则我们只能让t->fail指向根节点了。


![](http://i.imgur.com/hTm2Ttk.png)

我们按照广搜的思路来走一下。最开始的时候根节点root进队。

1、root出队，我们为root的儿子们找fail指针，通过检测我们发现当前是要给root的儿子们h,s找fail指针，故h,s直接指向root，如图中红色虚线所示。并且h,s依次进队。当前队列中有h,s。

2、接下来h出队，我们现在要给h的儿子e找fail指针，因此首先我们temp = h->fail，我们知道temp现在肯定
是root,然后会看root的孩子有没有字符是e的，发现是没有的，这时候已经到根了，没有办法往前再翻找了，所以
节点e的fail指针就指向了root。同时e进队。当前队列有s,e。

3、接下来出队的是s,我们现在要给s的儿子a,h找fail指针，按照顺序先给a找，同样temp = s->fail，则temp现在
指向根节点root,我们发现根节点的孩子是没有字符是a的，所以a的fail只能指向root。同时a进队，此时队列有e,a。然后找h的fail指针，同样temp = s->fail，则temp依然是根，我们发现根节点的孩子有字符是h的，则这个时候h的
fail指针就会指向第二层的h节点。然后h进队，当前队列e a h。第三层的节点的fail指针就都找完了，如图中蓝色虚线
所示。

4、接下来出队的e,然后该给e的孩子r找fail指针，同样temp = e->fail。则temp是根root当前，我们看根节点
下面没有字符是r的节点,所以r的fail会指向root,r进队，当前队里面有 a , h , r。

5、接下来出队的a,然后找a的孩子y的fail指针，可以发现情况和4这一条是相同的，所以y的fail指针也是指向
root的。同时y进队。当前队里面有h,r,y

6、接下来h出队，我们给h的孩子e,r找fail指针，按顺序先给e找，temp = h->fail。则当前temp就是第二层的
h，然后我们看temp的孩子有没有是字符e的，发现第二层的h其孩子有字符值是e的.则e的fail指针就指向图中第
三层的e,同时e进队，然后我们找r的fail，会发现r其情况与4，5是相同的，则r的fail指针指向root.并且r进队。
当前队中节点有：r,y,e,r。 第四层的所有点的fail指针已经找到，其指向如图中绿色虚线线条所示。

7、r出队，找r的孩子的fail指针，发现r没有孩子，那就不管了，后面的y,e,r都是没孩子的，依次出队就可以
了。

此时我们已经给字典树上各个节点求了fail指针，所以AC自动机已构建完毕。

求fail指针的代码部分

```
///求fail指针。构造AC自动机。    
void build_AC_automaton()    
{    
    TrieNode *p;    
    p = root;    
    queue<TrieNode*>qu;    
    qu.push(p);    
    while(!qu.empty())    
    {    
        p = qu.front();    
        qu.pop();    
        for(int i = 0; i < allSon; i++)    
        {    
            if(p->son[i] != NULL) ///第i个孩子存在    
            {    
                if(p == root)  ///p是根，根节点的孩子的失败指针都指向自己    
                {    
                    p->son[i]->fail = root;    
                }    
                else    
                {    
                    TrieNode *node = p->fail;     
                    while(node != NULL)    
                    {    
                        if(node->son[i]!=NULL)    
                        {    
                            p->son[i]->fail = node->son[i];    
                            break;    
                        }    
                        node = node->fail;    
                    }    
                    if(node == NULL)    
                        p->son[i]->fail = root;    
                }    
                qu.push(p->son[i]);    
            }    
        }    
    }    
}    

```

**文本串的匹配**

最后一步，让文本串在AV自动机上跑一遍。匹配过程分两种情况：

（1） 当前节点node与文本串上的字符匹配成功，我们要做的是检测当前节点是否有标记表示它是模式串中一个单词的结尾。如果是结尾的话，就说明当前节点到根这条路上字母组成的单词是一个完整的单词，我们统计的单词数就要加1.不管它是不是结尾，我们都要进行以下操作：

此时我们利用当前节点的fail指针，找到另一个节点temp=node->fail。如果发现它也有标记表明它是一个完整的单词，则我们就可以让统计的单词数加1.

（2） 如果当前节点node匹配上了文本串的一个字符，然后发现匹配下一个字符的时候失配了，那么我们就到node->fail处，因为【root，node->fail】是【root，node】的后缀，我们知道【root，node->fail】这一段还是根据文本串中的一段是匹配的，我们看node->fail它的孩子能不能跟我们失配的字符匹配上，如果能匹配上，就往前找node->fail-fail。如果配不上，我们就执行（1），一直进行这个过程，知道找完为止。

### **AC自动机的代码模板** ###

```
#include <iostream>    
#include <stdio.h>    
#include <stdlib.h>    
#include <queue>    
    
using namespace std;    
    
const int allSon=26;    
char patten[60];      ///模式串    
char text[1000010];   ///文本串    
int ans;    
struct TrieNode    
{    
    struct TrieNode *son[allSon];  ///儿子们    
    struct TrieNode *fail;         ///匹配失败时候的指针指向    
    int num;                       ///以该节点所代表字符串为结尾的单词数    
}*root;    
///创建节点    
TrieNode* createNode()    
{    
    TrieNode *p;    
    p = (TrieNode*)malloc(sizeof(TrieNode));    
    for(int i = 0; i < allSon; i++) p->son[i] = NULL;    
    p->num = 0;    
    p->fail = NULL;    
    return p;    
}    
///插入模式串，构建字典树    
void insertPatten()    
{    
    TrieNode *p;    
    p = root;    
    int index = 0;    
    while(patten[index] != '\0')    
    {    
        int lowercase = patten[index]-'a';    
        if(p->son[lowercase]==NULL)      
        {    
            p->son[lowercase] = createNode();    
        }    
        p = p->son[lowercase];    
        index++;    
    }    
    p->num++;    
}    
///求fail指针。构造AC自动机。    
void build_AC_automaton()    
{    
    TrieNode *p;    
    p = root;    
    queue<TrieNode*>qu;    
    qu.push(p);    
    while(!qu.empty())    
    {    
        p = qu.front();    
        qu.pop();    
        for(int i = 0; i < allSon; i++)    
        {    
            if(p->son[i] != NULL) ///第i个孩子存在    
            {    
                if(p == root)  ///p是根，根节点的孩子的失败指针都指向自己    
                {    
                    p->son[i]->fail = root;    
                }    
                else    
                {    
                    TrieNode *node = p->fail;     
                    while(node != NULL)    
                    {    
                        if(node->son[i]!=NULL)    
                        {    
                            p->son[i]->fail = node->son[i];    
                            break;    
                        }    
                        node = node->fail;    
                    }    
                    if(node == NULL)    
                        p->son[i]->fail = root;    
                }    
                qu.push(p->son[i]);    
            }    
        }    
    }    
}    
void find_in_AC_automaton()    
{    
    TrieNode *p;    
    p = root;    
    int index = 0;    
    while(text[index] != '\0')    
    {    
        int lowercase = text[index]-'a';    
        while(p->son[lowercase]==NULL && p!=root)    
            p = p->fail;   ///失配，转到能配的地方再尝试匹配    
        p = p->son[lowercase];    
        if(p == NULL) p = root;     
        TrieNode *temp = p; ///把那些以当前节点的后缀作为后缀的字符串统计了。    
        while(temp!=NULL && temp->num!=-1)    
        {    
            ans += temp->num;    
            temp->num = -1;    
            temp = temp->fail;    
        }    
        index++;    
    }    
}    
///记得释放接内存，用完及时归还系统，不然会爆的。    
void freeNode(TrieNode *node)    
{    
    if(node != NULL)    
    {    
        for(int i = 0; i < allSon; i++)    
            freeNode(node->son[i]);    
    }    
    free(node);    
}    
int main()    
{    
    int t,n;    
    scanf("%d",&t);    
    while(t--)    
    {    
        scanf("%d",&n);    
        root = createNode();    
        for(int i = 0; i < n; i++)    
        {    
            scanf("%s",patten);    
            insertPatten();   ///用模式串构建字典树    
        }    
        scanf("%s",text);    
        build_AC_automaton(); ///构建AC自动机     
        ans = 0;    
        find_in_AC_automaton(); ///多模式匹配    
        printf("%d\n",ans);    
        freeNode(root);        ///释放内存    
    }    
    return 0;    
}    
```