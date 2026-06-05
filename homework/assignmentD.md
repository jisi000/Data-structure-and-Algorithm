# DSA Assignment D: 20260527模拟考

*Updated 2026-05-20 16:47 GMT+8*
 *Compiled by <mark>孙婧斯、生命科学学院</mark> (2026 Spring)*



>**说明：**
>
>1. **解题与记录：**
>
>     对于每一个题目，请提供其解题思路（可选），并附上使用Python或C++编写的源代码（确保已在OpenJudge， Codeforces，LeetCode等平台上获得Accepted）。请将这些信息连同显示“Accepted”的截图一起填写到下方的作业模板中。（推荐使用Typora https://typoraio.cn 进行编辑，当然你也可以选择Word。）无论题目是否已通过，请标明每个题目大致花费的时间。
>
>2. **提交安排：**提交时，请首先上传PDF格式的文件，并将.md或.doc格式的文件作为附件上传至右侧的“作业评论”区。确保你的Canvas账户有一个清晰可见的本人头像，提交的文件为PDF格式，并且“作业评论”区包含上传的.md或.doc附件。
> 
>3. **延迟提交：**如果你预计无法在截止日期前提交作业，请提前告知具体原因。这有助于我们了解情况并可能为你提供适当的延期或其他帮助。  
>
>请按照上述指导认真准备和提交作业，以保证顺利完成课程要求。



## 1. 题目

### M27351:01最小生成树

补图的连通分量, http://cs101.openjudge.cn/practice/27351

思路：先把没有规定边的节点都连接好，这一步使用了BFS。每次从剩余的节点中随机pop()一个，然后探索整个连通块。

得到一些连通块后，把他们缩成新的节点，然后使用kruskal算法计算最小总边权。



代码：

```python
import sys
sys.setrecursionlimit(10**6)
class UnionFind:
    def __init__(self, n):
        self.parent=list(range(n))
        self.rank=[0]*n
        self.n=n
    def find(self,x):
        if self.parent[x]!=x:
            self.parent[x]=self.find(self.parent[x])
        return self.parent[x]
    def union(self,x,y):
        rx=self.find(x)
        ry=self.find(y)
        if rx==ry:
            return False
        if self.rank[rx]<self.rank[ry]:
            self.parent[rx]=ry
        elif self.rank[rx]>self.rank[ry]:
            self.parent[ry]=rx
        else:
            self.parent[rx]=ry
            self.rank[ry]+=1
        return True
    def count(self):
        switch_id={}
        count=0
        for i in range(self.n):
            root=self.find(i)
            if root not in switch_id:
                switch_id[root]=count
                count+=1
        return switch_id,count
from collections import defaultdict,deque
n,m=map(int,input().split())
uf1=UnionFind(n)
edges=[]
assign=defaultdict(set)
for _ in range(m):
    u,v=map(int,input().split())
    u-=1
    v-=1
    edges.append((u,v))
    assign[u].add(v)
    assign[v].add(u)
unvisited=set(range(n))
while unvisited:
    start=unvisited.pop()
    queue=deque([start])
    while queue:
        u=queue.popleft()
        re=list(unvisited)
        for v in re:
            if v not in assign[u]:
                uf1.union(u,v)
                unvisited.remove(v)
                queue.append(v)
com_id,com_count=uf1.count()
uf2=UnionFind(com_count)
weight=0
for u,v in edges:
    cu=com_id[uf1.find(u)]
    cv=com_id[uf1.find(v)]
    if uf2.union(cu,cv):
        weight+=1
        com_count-=1
        if com_count==1:
            break
print(weight)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260528154827475](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260528154827475.png)



### M30910:邮递员送快递

正向/反向图 Dijkstra, http://cs101.openjudge.cn/practice/30910


思路：dijkstra算法能高效的得到从一点到其他所有点的最短距离。利用这个性质，在查找回来的路时，把图反转，从0开始查找。



代码：

```python
n,m=map(int,input().split())
adj1=[[] for _ in range(n)]
adj2=[[] for _ in range(n)]
for _ in range(m):
    u,v,w=map(int,input().split())
    u-=1
    v-=1
    adj1[u].append((v,w))
    adj2[v].append((u,w))
import heapq
def dijkstra(adj,start):
    dist=[float('inf')]*n
    dist[start]=0
    pq=[(0,start)]
    while pq:
        d,u=heapq.heappop(pq)
        if d>dist[u]:
            continue
        for v,w in adj[u]:
            if dist[u]+w<dist[v]:
                dist[v]=dist[u]+w
                heapq.heappush(pq,(dist[v],v))
    return dist
a=sum(dijkstra(adj1,0))
b=sum(dijkstra(adj2,0))
print(a+b)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260528163917296](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260528163917296.png)



### M30912:累加树

构建 BST + 右-根-左累加 + BFS 输出, http://cs101.openjudge.cn/practice/30912

思路：看群里说这题的数据有问题，考试的时候我看给的是二叉搜索树的前序遍历，猜想题目想让我一个数一个数依次插入，侥幸AC了。

二叉搜索树的中序遍历就是从小到大的排序，那么每个节点的新节点值 = 中序遍历中前一个数字的新节点值 + 本身的值

最后用BFS输出层序遍历结果

代码：

```python
class Node:
    def __init__(self,val=None):
        self.val = val
        self.left=None
        self.right=None
n_i={}
i_n=[]
def insert(node,num):
    if not node:
        re=Node(num)
        i_n.append(re)
        n_i[re]=len(i_n)-1
        return re
    if node.val>num:
        node.left=insert(node.left,num)
    elif node.val<num:
        node.right=insert(node.right,num)
    return node
n=int(input())
nums=list(map(int,input().split()))
root=None
for num in nums:
    root=insert(root,num)
mid_seq=[]
def mid(node):
    if node:
        mid(node.right)
        mid_seq.append(n_i[node])
        mid(node.left)
mid(root)
ans=[-1]*n
ans[mid_seq[0]]=i_n[mid_seq[0]].val
for i in range(1,n):
    val=i_n[mid_seq[i]].val
    ans[mid_seq[i]]=val+ans[mid_seq[i-1]]
from collections import deque
def sheer(node):
    queue=deque([node])
    l=[]
    while queue:
        re=queue.popleft()
        l.append(ans[n_i[re]])
        if re.left:
            queue.append(re.left)
        if re.right:
            queue.append(re.right)
    return l
print(*sheer(root))
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260528164626741](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260528164626741.png)



### M30899:火星大工程

关键路径, http://cs101.openjudge.cn/practice/30899

思路：一个很标准的关键路径题，用学的模板就可以解决。

核心原理是：最早开始时间和最晚开始时间相同的活动在关键路径上。

代码：

```python
import sys
from collections import defaultdict,deque
sys.setrecursionlimit(10**6)
class Edge:
    def __init__(self,v,w):
        self.v=v
        self.w=w
def topo_sort(n,graph,in_degree):
    q=deque([i for i in range(n)])
    ve=[0]*n
    topo_order=[]
    while q:
        u=q.popleft()
        topo_order.append(u)
        for edge in graph[u]:
            v=edge.v
            if ve[u]+edge.w>ve[v]:
                ve[v]=ve[u]+edge.w
            in_degree[v]-=1
            if in_degree[v]==0:
                q.append(v)
    return ve,topo_order
def get_critical_path(n,graph,in_degree):
    ve,topo_order=topo_sort(n,graph,in_degree)
    maxL=max(ve)
    vl=[maxL]*n
    for u in reversed(topo_order):
        for edge in graph[u]:
            v=edge.v
            if vl[v]-edge.w<vl[u]:
                vl[u]=vl[v]-edge.w
    activity=[]
    for u in range(n):
        for edge in graph[u]:
            v=edge.v
            e=ve[u]
            l=vl[v]-edge.w
            if e==l:
                activity.append((u+1,v+1))
    return maxL,activity
def main():
    data=sys.stdin.buffer.read().split()
    it=iter(data)
    n=int(next(it))
    m=int(next(it))
    graph=defaultdict(list)
    in_degree=[0]*n
    for _ in range(m):
        u=int(next(it))-1
        v=int(next(it))-1
        w=int(next(it))
        graph[u].append(Edge(v,w))
        in_degree[v]+=1
    in_degree_copy=in_degree[:]
    maxL,activity=get_critical_path(n,graph,in_degree_copy)
    print(maxL)
    activity.sort()
    for path in activity:
        print(*path)
if __name__=='__main__':
    main()
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260528165427135](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260528165427135.png)



### T30868:upstairs

同余最短路, http://cs101.openjudge.cn/practice/30868

思路：以x为基准，计算出通过y和z凑得的余x得i（0<=i<=x-1）的最小楼层。然后对输入的楼层余x，看看楼层高度是否 >= 该余数的最小楼层。

由于备选数字最多只有三个，因此没有建图，直接在dijkstra中对每个节点遍历所有可能的步长。

代码

```python
import heapq
import sys
def data():
    for line in sys.stdin.buffer:
        for token in line.split():
            yield token
def solve():
    it=data()
    try:
    	a=int(next(it))
    	b=int(next(it))
    	c=int(next(it))
    except StopIteration:
        return
    steps=[num for num in [a,b,c] if num!=0]
    if not steps:
        try:
        	t=int(next(it))
        	ans=[]
        	for _ in range(t):
            	f=int(next(it))
            	if f==0:
                	ans.append('Yes')
            	else:
                	ans.append('No')
        	print('\n'.join(ans))
        except StopIteration:
            pass
        return
    steps.sort()
    x=steps[0]
    d=[float('inf')]*x
    visited=[False]*x
    d[0]=0
    q=[(0,0)]
    while q:
        dist,u=heapq.heappop(q)
        if visited[u]:
            continue
        visited[u]=True
        for num in steps:
            v=(u+num)%x
            if d[v]>d[u]=num:
                d[v]=d[u]+num
                heapq.heappush(q,(d[v],v))
    try:
    	t=int(next(it))
    	ans=[]
    	for _ in range(t):
        	f=int(next(it))
        	j=f%x
        	if f>=d[j]:
            	ans.append('Yes')
        	else:
            	ans.append('No')
    	print('\n'.join(ans))
    except StopIteration:
        pass
if __name__=='__main__':
    solve()
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260529111629154](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260529111629154.png)



### T30921:猫猫搭积木

并查集, http://cs101.openjudge.cn/practice/30921

思路：基本思路是并查集，动态维护count，实时计算积木堆的数量，同时把倒塌的堆解散。为了防止频繁的解散操作超时，使用懒标记的技巧，多开2*q的空间，保证每次输入都有解散操作也够用。本质上是在用空间换时间。

real_id储存被替换过编号的积木块初始编号，new_id储存每个初始编号经替换后最新的编号。safe_place是一个动态的id，用于记录还没被使用的编号。destroyed是一个不会被积木使用到的编号，如果parent[x]指向destroyed，说明此编号已经废弃。为了destroyed访问不越界，parent、num、rank数组需要多开一位，记录初始编号的real_id数组就不需要。

代码

```python
class UnionFind:
    def __init__(self,n,q,s):
        self.s=s
        self.destroyed=n+2*q
        self.parent=list(range(n+2*q+1))
        self.rank=[0]*(n+2*q+1)
        self.num=[1]*(n+2*q+1)
        self.count=n
        self.init_id=list(range(n))
        self.real_id=list(range(n+2*q))
        self.safe_place=n
    def _find(self,x):
        if self.parent[x]!=x:
            self.parent[x]=self._find(self.parent[x])
        return self.parent[x]
    def find(self,x):
        if self._find(x)==self.destroyed:
            real_id=self.real_id[x]
            self.init_id[real_id]=self.safe_place
            self.real_id[self.safe_place]=real_id
            x=self.safe_place
            self.safe_place+=1
        return self.parent[x]
    def union(self,x,y):
        x,y=self.init_id[x],self.init_id[y]
        rx=self.find(x)
        ry=self.find(y)
        if rx!=ry:
            to_test=rx
            if self.rank[rx]<self.rank[ry]:
                self.parent[rx]=ry
                self.num[ry]+=self.num[rx]
                to_test=ry
            elif self.rank[rx]>self.rank[ry]:
                self.parent[ry]=rx
                self.num[rx]+=self.num[ry]
            else:
                self.parent[ry]=rx
                self.num[rx]+=self.num[ry]
                self.rank[rx]+=1
            self.count-=1
            if self.num[to_test]>=self.s:
                self.count+=(self.num[to_test]-1)
                self.parent[to_test]=self.destroyed
n,q,s=map(int,input().split())
uf=UnionFind(n,q,s)
for _ in range(q):
    a,b=map(int,input().split())
    uf.union(a-1,b-1)
    print(uf.count)
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260529115648783](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260529115648783.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

本次模考AC4，只做出来了4道M题。

猫猫搭积木 没有想到解散操作的优化方式，一直在超时，考试后学习了一下群里分享的思路。

upstairs 由于没有按时做选做题，考试时还没有学到同余最短路问题，考试后学了一下模板。

感谢选做题在5.28戛然而止使我来得及全都补上，整理了最终版cheat sheet，涉及的知识点都是我这学期在做作业和选做题中用到的。

https://github.com/jisi000/Data-structure-and-Algorithm/blob/main/cheat%20sheet.md

