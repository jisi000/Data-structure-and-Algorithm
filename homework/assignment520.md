# DSA Assignment 520: 20260520模拟考

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

### E04080: Huffman编码树

http://cs101.openjudge.cn/practice/04080/

思路：哈夫曼编码树的建树模板+树的遍历



代码：

```python
import heapq
class Node:
    def __init__(self,weight):
        self.weight=weight
        self.left=None
        self.right=None
    def __lt__(self,other):
        return self.weight<other.weight
heap=[]
n=int(input())
arr=list(map(int,input().split()))
for num in arr:
    node=Node(num)
    heapq.heappush(heap,node)
while len(heap)>1:
    node1=heapq.heappop(heap)
    node2=heapq.heappop(heap)
    n_weight=node1.weight+node2.weight
    n_node=Node(n_weight)
    n_node.left=node1
    n_node.right=node2
    heapq.heappush(heap,n_node)
root=heap[0]
total=[0]
def traversal(node,depth):
    if not node.right and not node.left:
        total[0]+=node.weight*depth
        return
    if node.left:
        traversal(node.left,depth+1)
    if node.right:
        traversal(node.right,depth+1)
traversal(root,0)
print(total[0])
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260521140509475](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260521140509475.png)



### M05443: 兔子与樱花

dijkstra, Floyd-Warshall, http://cs101.openjudge.cn/practice/05443/


思路：dijkstra+恢复路径

恢复路径：记录每个点的上一个点，到达终点时逆推还原

代码：

```python
import heapq
P=int(input())
name_to_index={}
index_to_name=[]
for i in range(P):
    name=input()
    name_to_index[name]=i
    index_to_name.append(name)
Q=int(input())
graph=[[-1]*P for _ in range(P)]
for _ in range(Q):
    a,b,w=input().split()
    graph[name_to_index[a]][name_to_index[b]]=int(w)
    graph[name_to_index[b]][name_to_index[a]]=int(w)
def dijkstra(start,end):
    dist=[float('inf')]*P
    dist[start]=0
    pq=[(0,start)]
    last=[-1]*P
    while pq:
        d,u=heapq.heappop(pq)
        if u==end:
            path=[index_to_name[u]]
            re=u
            while re!=-1:
                if re==start:
                    break
                v=last[re]
                w=graph[re][v]
                path.append(f'({w})')
                path.append(index_to_name[v])
                re=v
            return path[::-1]
        if d>dist[u]:
            continue
        for v in range(P):
            w=graph[u][v]
            if w!=-1 and dist[u]+w<dist[v]:
                dist[v]=dist[u]+w
                heapq.heappush(pq,(dist[v],v))
                last[v]=u
R=int(input())
for _ in range(R):
    n1,n2=input().split()
    pth=dijkstra(name_to_index[n1],name_to_index[n2])
    print('->'.join(pth))
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>



![image-20260521140539005](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260521140539005.png)

### M20741: 两座孤岛最短距离

bfs, http://cs101.openjudge.cn/practice/20741/

思路：先标记一个岛屿上的所有点，再尝试从这个岛上的任意点出发寻找另外的岛屿，岛屿内移动步数+0

代码：

```python
from collections import deque
n=int(input())
graph=[list(map(int,list(input()))) for _ in range(n)]
d=[(0,1),(1,0),(-1,0),(0,-1)]
def signal(x0,y0,sign):
    queue=deque([(x0,y0)])
    region={(x0,y0)}
    graph[x0][y0]=sign
    while queue:
        x,y=queue.popleft()
        for dx,dy in d:
            a,b=x+dx,y+dy
            if 0<=a<n and 0<=b<n and graph[a][b]==1:
                graph[a][b]=sign
                queue.append((a,b))
                region.add((a,b))
    return region
def bfs(region):
    dist=[[float('inf')]*n for _ in range(n)]
    queue=deque()
    for x,y in region:
        dist[x][y]=0
        queue.append((x,y))
    while queue:
        x,y=queue.popleft()
        for dx,dy in d:
            a,b=x+dx,y+dy
            if 0<=a<n and 0<=b<n:
                cost=dist[x][y]+(1 if graph[a][b]==0 else 0)
                if cost<dist[a][b]:
                    dist[a][b]=cost
                    queue.append((a,b))
    return dist
region2,region3=None,None
for i in range(n):
    for j in range(n):
        if graph[i][j]==1:
            if not region2:
                region2=signal(i,j,2)
            else:
                region3=signal(i,j,3)
dis=bfs(region2)
ans=float('inf')
for x,y in region3:
    ans=min(ans,dis[x][y])
print(ans)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260521140600882](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260521140600882.png)



### M24637: 宝藏二叉树

dp, dfs http://cs101.openjudge.cn/practice/24637/

思路：用两个数组，一个代表使用当前节点，因此不得使用子节点，一个代表不使用当前节点，因此可以从选不选左右子节点中选最大值。



代码：

```python
N=int(input())
t=list(map(int,input().split()))
dp1=[0]*N
dp2=[0]*N
for i in range(N-1,-1,-1):
    if 2*i+2<N:
        dp1[i]=t[i]+dp2[2*i+1]+dp2[2*i+2]
        dp2[i]=max(dp2[2*i+1]+dp1[2*i+2],dp1[2*i+1]+dp2[2*i+2],dp1[2*i+1]+dp1[2*i+2],dp2[2*i+1]+dp2[2*i+2])
    elif 2*i+1<N:
        dp1[i]=t[i]
        dp2[i]=dp1[2*i+1]
    else:
        dp1[i]=t[i]
print(max(dp1[0],dp2[0]))
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260521140622336](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260521140622336.png)



### T02337: Catenyms

Eulerian Path, http://cs101.openjudge.cn/practice/02337/

思路：把单词当作首字母到尾字母的路径，最终应形成要么是一条链，要么是一条环的情况。用并查集判断全局连通性，不连通就提前终止。统计入度出度，如果不符合（一个入点&一个出点&其余点出入度相等）or（所有点出入度相等）的情况，都可以提前终止。

如果判断都通过了，递归构建路径。至于为什么dfs函数中用while而不是if，

假设：  a → b → c

​	             ↓↑

​                      d

如果用if就会忽略掉支路

为了维护字典序最小，需要根据单词对graph中的（边，点）元组排序。因为使用方法是pop()，因此排成逆序。

代码

```python
import sys
sys.setrecursionlimit(10**6)
class UnionFind:
    def __init__(self):
        self.parent=list(range(26))
    def find(self,x):
        if x!=self.parent[x]:
            self.parent[x]=self.find(self.parent[x])
        return self.parent[x]
    def union(self,x,y):
        px,py=self.find(x),self.find(y)
        if px!=py:
            self.parent[px]=py
def dfs(u):
    while graph[u]:
        w,v=graph[u].pop()
        dfs(v)
        path.append(w)

data=sys.stdin.buffer.read().split()
it=iter(data)
t=int(next(it))
for _ in range(t):
    n=int(next(it))
    uf=UnionFind()
    in_degree=[0]*26
    out_degree=[0]*26
    used=[False]*26
    graph=[[] for _ in range(26)]
    for i in range(n):
        word=next(it).decode()
        u=ord(word[0])-97
        v=ord(word[-1])-97
        uf.union(u,v)
        in_degree[v]+=1
        out_degree[u]+=1
        graph[u].append((word,v))
        used[u]=True
        used[v]=True
    for i in range(26):
        graph[i].sort(reverse=True)
    ok=True
    fa=-1
    for i in range(26):
        if used[i]:
            if fa==-1:
                fa=uf.find(i)
            else:
                if uf.find(i)!=fa:
                    ok=False
    start=-1
    inc,ouc=0,0
    for i in range(26):
        if used[i]:
            if in_degree[i]-out_degree[i]==1:
                ouc+=1
            elif in_degree[i]-out_degree[i]==-1:
                inc+=1
                start=i
            else:
                if in_degree[i]-out_degree[i]!=0:
                    ok=False
    if not ((inc==1 and ouc==1) or (ouc==0 and inc==0)):
        ok=False
    if not ok:
        print('***')
    else:
        if start==-1:
            for i in range(26):
                if used[i]:
                    start=i
                    break
        path=[]
        dfs(start)
        if len(path)!=n:
            print('***')
        else:
            print('.'.join(path[::-1]))
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260521140733119](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260521140733119.png)



### T30878:力场叠加模拟

segment tree, lazy propagation, http://cs101.openjudge.cn/practice/30878/

思路：



代码

```python

```



<mark>（至少包含有"Accepted"）</mark>





## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

AC5，前四题都是已经掌握的模板或者以前做过的题，很快做完。

第五题没见过，一开始把单词当作点，相同的字母当作边，直接dfs暴力查找，果然超时了。不过也在写代码的过程中发现单词中间的字母对结果完全无影响，于是思考能不能把单词首尾字母当作点，单词作为边。然后突然发现这样就很方便了，直接用并查集和出入度统计来判断能否建图。然后使用dfs构建路径。

但是WA，此时在5、6题之间决策了一下，因为明确知道期末不考线段树，所以一直没有去学，而第6题用我会写的树状数组解决不了，那么第6题可以直接放弃了。

于是回看第五题构建路径的思路，我一开始写的是 if graph[u]: ，因为即使一个点有很多边，但是根据图的连通性，一个标准的图一定会重新回到这一点，然后继续使用其他边。自己写了一些例子尝试找问题，包括上面写的那个。于是发现 if 保证能构建主干，但是对自身是环，而且字典序比主干靠后的侧枝， if 会完全忽略掉，因为直接走主干就回不到这一点了。此时已经马上就到五点了，于是直接尝试把 if 改成 while ，提交，幸运的卡点AC了。

考试结束后根据tag搜索了一下欧拉路径，学习了一下使用while的理论依据。

最近每门课任务都很多因此还是没有写线段树那道题，总之机考不考就暂时放过它了。  



