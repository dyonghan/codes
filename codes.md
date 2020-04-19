# 算法

[二叉树遍历](https://www.cnblogs.com/shunyu/p/4986288.html)
[BFS, DFS](https://www.cnblogs.com/whywhy/p/4888632.html)
[](https://blog.csdn.net/u013834525/article/details/82992097)

## 链表

### 复杂链表复制

方法一：map关联
    首先遍历一遍原链表，创建新链表（赋值label和next），用map关联对应结点；再遍历一遍，更新新链表的random指针。（注意map中应有NULL ----> NULL的映射）

方法二：next指针关联
    创建新链表的时候，用原结点的next指针指向对应新结点，新结点的next指针指向下一个原结点，以此类推，形成之字形关联。然后，就可以先更新新链表的random指针，再解除next关联，更新next指针。这种方法不需要map来辅助，不管查找next还是random指针都是O(1)的，效率很高。

```c++

class Solution {
public:
    RandomListNode* Clone(RandomListNode* pHead)
    {
        if(pHead==NULL) return NULL;
 
        map<RandomListNode*,RandomListNode*> m;
        RandomListNode* pHead1 = pHead;
        RandomListNode* pHead2 = new RandomListNode(pHead1->label);
        RandomListNode* newHead = pHead2;
        m[pHead1] = pHead2;
        while(pHead1){
            if(pHead1->next) pHead2->next = new RandomListNode(pHead1->next->label);
            else pHead2->next = NULL;
            pHead1 = pHead1->next;
            pHead2 = pHead2->next;
            m[pHead1] = pHead2;
        }
 
        pHead1 = pHead;
        pHead2 = newHead;
        while(pHead1){
            pHead2->random = m[pHead1->random];
            pHead1 = pHead1->next;
            pHead2 = pHead2->next;
        }
        return newHead;
    }
};


// 感谢你灵感，然后让我改了下自己的代码，让它变非常精简了。
// 递归思想，不过上面给的答案递归都有问题。我的改正了。
class Solution {
public:
    map<RandomListNode*,RandomListNode*> nodemap;
    RandomListNode* Clone(RandomListNode* pHead)
    {
       if(pHead == NULL) return NULL;
        RandomListNode* phead=new RandomListNode(pHead->label);
        nodemap[pHead]=phead;
        phead->next=Clone(pHead->next);  //在这里递归是关键，保证所有节点都已生成，放入map
        phead->random=NULL;
        if( pHead->random) phead->random=nodemap[pHead->random];   //查找map中对应节点
        return phead;
    }
};

// 因为题目上说random指向任意节点，不知道这个random指向的节点是否一定在链表中，所以直接当做一个有向图来做了，做法大同小异。
class Solution {
    map<RandomListNode*, RandomListNode*> mp;
    set<RandomListNode*> vis;
    void dfs1(RandomListNode* u){
        if(u && mp.find(u) == mp.end()) {
            mp[u] = new RandomListNode(u -> label);
            dfs1(u -> next);
            dfs1(u -> random);
        }
    }
    void dfs2(RandomListNode* u){
        if(u && vis.find(u) == vis.end()){
            if(u -> next) mp[u] -> next = mp[u -> next];
            if(u -> random) mp[u] -> random = mp[u -> random];
            vis.insert(u);
            dfs2(u -> next);
            dfs2(u -> random);
        }
    }
public:
    RandomListNode* Clone(RandomListNode* pHead){
        if(!pHead) return NULL;
        mp.clear();
        vis.clear();
        dfs1(pHead);
        dfs2(pHead);
        return mp[pHead];
    }
};
// 针对这个题来说，第二个dfs1和第二个dfs2都可以省略，因为链表通过next指针可以全部遍历，没必要第二次调用dfs1、dfs2。当然，如果把它推广成图，next指针不一定能完全遍历，你的代码通用性更强。
// 因为复制就相当于从旧链表到新链表是一个一一对应的映射，就可以用map先建立好结点的映射关系，然后按照原链表对新结点的指针赋值（边的映射关系）。这里的set只是一个标记结点是否已经访问过的工具。


public class Solution {
    public RandomListNode Clone(RandomListNode pHead){
        if(pHead==null)
            return null;
        RandomListNode pCur = pHead;
        //复制next 如原来是A->B->C 变成A->A'->B->B'->C->C'
        while(pCur!=null){
            RandomListNode node = new RandomListNode(pCur.label);
            node.next = pCur.next;
            pCur.next = node;
            pCur = node.next;
        }
        pCur = pHead;
        //复制random pCur是原来链表的结点 pCur.next是复制pCur的结点
        while(pCur!=null){
            if(pCur.random!=null)
                pCur.next.random = pCur.random.next;
            pCur = pCur.next.next;
        }
        RandomListNode head = pHead.next;
        RandomListNode cur = head;
        pCur = pHead;
        //拆分链表
        while(pCur!=null){
            pCur.next = pCur.next.next;
            if(cur.next!=null)
                cur.next = cur.next.next;
            cur = cur.next;
            pCur = pCur.next;
        }
        return head;       
    }
}

ublic class Solution {
    public RandomListNode Clone(RandomListNode pHead)
    {
        if (pHead == null) return null;
         
        RandomListNode pCur = pHead;
        while (pCur != null)
        {
            RandomListNode node = new RandomListNode(pCur.label);
            node.next = pCur.next;
            pCur.next = node;
            pCur = node.next;
        }
         
        pCur = pHead;
        while (pCur!=null)
        {
            if (pCur.random!=null)
                pCur.next.random = pCur.random.next;
            pCur = pCur.next.next;
        }
         
        RandomListNode head = pHead.next;
        RandomListNode tmp = head;
        pCur = pHead;
        while(pCur.next!=null)
        {
            tmp = pCur.next;
            pCur.next = tmp.next;
            pCur = tmp;
        }
        return head;
    }
}

```

### 交汇链表

两个链表交汇后，后续部分为一条，求交汇点

```py
#coding=utf-8
import sys 

class link():
    def __init__(label):
        self.label=label
        self.next=None
        
        
def find():
    link1 = link()
    link2 = link()
    len1,len2 = 0,0
    cusor1, cusor2 = link1, link2
    while cusor1.next:
        len1+=1
        cusor1 = cusor1.next
    while cusor2.next:
        len2+=1
        cusor2 = cusor2.next
        
    diff = len2 - len1
    cusor1, cusor2 = link1, link2
    if diff >=0:
        for i in xrange(diff):
            cusor2 = cusor2.next
    else:
        for i in xrange(-diff):
            cusor1 = cusor1.next
            
    while cusor1.label != cusor2.label:
        cusor1 = cusor1.next
        cusor2 = cusor2.next
        
    return cusor1.label

```

## 排序

### 排序合并

m个升序数组，合并为一个升序数组

```py
#coding=utf-8
import sys 

aa = []
m = len(aa)
lenaa = sum((len(aa[x]) for x in aa))
ret = []
cusor = [0 for x in aa]

for j in xrange(lenaa):
    for i in xrange(m):
        dmin = 0
        chosed = -1
        if (cusor[i] < len(aa[i])):
            if aa[i][cusor[i]] < dmin:
                dmin = aa[i][cusor[i]]
                chosed = i
    cusor[chosed] + = 1
    ret.append(dmin)
```

## 天际线

P为给定的二维平面整数点集。定义 P 中某点x，如果x满足 P 中任意点都不在 x 的右上方区域内（横纵坐标都大于x），则称其为“最大的”。求出所有“最大的”点的集合。（所有点的横坐标和纵坐标都不重复, 坐标轴范围在[0, 1e9) 内）

如下图：实心点为满足条件的点的集合。请实现代码找到集合 P 中的所有 ”最大“ 点的集合并输出。

输入描述:
第一行输入点集的个数 N， 接下来 N 行，每行两个数字代表点的 X 轴和 Y 轴。
对于 50%的数据,  1 <= N <= 10000;
对于 100%的数据, 1 <= N <= 500000;

输出描述:
输出“最大的” 点集合， 按照 X 轴从小到大的方式输出，每行两个数字分别代表点的 X 轴和 Y轴。

输入例子1:
5
1 2
5 3
4 6
7 5
9 0

输出例子1:
4 6
7 5
9 0

```c++
#include <iostream>
#include <cstdio>
#include <algorithm>
using namespace std;
struct nn{
    int x,y;
}qq[500005];
bool cmp(nn a,nn b){
    return a.y>b.y;
}
int main(){
    int n,ans=0,maxx=-1;
    cin>>n;
    for(int i=0;i<n;i++)
        scanf("%d%d",&qq[i].x,&qq[i].y);
    sort(qq,qq+n,cmp); // y降序排列
    for(int i=0;i<n;i++)
        if(qq[i].x>maxx){
            printf("%d %d\n",qq[i].x,qq[i].y);
            maxx=qq[i].x;//更新最大的x坐标
        }
}
```

```py
n=int(input())
a=[]
for i in range(n):
    a.append([int(j) for j in input().split()])
# y降序排列
a.sort(key=lambda x: x[1], reverse=True)
maxx=-1
for i in range(n):
    if a[i][0]>maxx:
        print('%s %s' % (a[i][0], a[i][1]))
        maxx = a[i][0]
```

## bfs

```py
visit = []
def dfs(node):
    queue = []
    queue.append(node)
    while queue:
        cur = queue.pop()
        visit.append(cur)
        for nd in cur.nexts():
            queue.insert(0, nd)
```



