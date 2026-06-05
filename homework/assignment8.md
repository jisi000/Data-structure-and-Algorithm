# DSA Assignment #8: 🌲（3/3）

*Updated 2026-04-21 19:09 GMT+8*
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

### M晴问9.7: 向下调整构建大顶堆

手搓堆, https://sunnywhy.com/sfbj/9/7

思路：因为是完全二叉树，所以可以通过索引找到节点的子节点。向下调整时，将节点值与子节点中最大的值调换（如果节点值比最大的子节点值小）。每个节点向下调整时，只有当子树都符合大顶堆结构，这次调整才是严谨的，因此应从尾向头遍历。



代码：

```python
n=int(input())
arr=list(map(int,input().split()))
def up_to_down(i):
    while i*2+1<n:
        if i*2+2>=n:
            child=i*2+1
        else:
            if arr[i*2+1]>=arr[i*2+2]:
                child=i*2+1
            else:
                child=i*2+2
        if arr[i]<arr[child]:
            arr[i],arr[child]=arr[child],arr[i]
            i=child
        else:
            break
for i in range(n-1,-1,-1):
    up_to_down(i)
print(*arr)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260421220259881](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260421220259881.png)



### M1722.执行交换操作后的最小汉明距离

dsu, https://leetcode.cn/problems/minimize-hamming-distance-after-swap-operations/

思路：用并查集合并能连通的位点，遍历source记录根下的数字（考虑到数字可能重复，记录数字的数量），遍历target在根下查找目标数字，没有找到汉明距离就加一。



代码：

```python
class UnionFind:
    def __init__(self,n):
        self.parent=list(range(n))
        self.rank=[0]*n
    def find(self,x):
        if self.parent[x]!=x:
            self.parent[x]=self.find(self.parent[x])
        return self.parent[x]
    def union(self,x,y):
        rx,ry=self.find(x),self.find(y)
        if rx!=ry:
            if self.rank[rx]>self.rank[ry]:
                self.parent[ry]=rx
            elif self.rank[ry]>self.rank[rx]:
                self.parent[rx]=ry
            else:
                self.parent[ry]=rx
                self.rank[rx]+=1

class Solution:
    def minimumHammingDistance(self, source: List[int], target: List[int], allowedSwaps: List[List[int]]) -> int:
        N=len(source)
        uf=UnionFind(N)
        for i,j in allowedSwaps:
            uf.union(i,j)
        d={}
        for i in range(N):
            r=uf.find(i)
            if r not in d:
                d[r]={}
            if source[i] not in d[r]:
                d[r][source[i]]=0
            d[r][source[i]]+=1
        ans=0
        for i in range(N):
            r=uf.find(i)
            if target[i] in d[r]:
                d[r][target[i]]-=1
                if d[r][target[i]]==0:
                    del d[r][target[i]]
            else:
                ans+=1
        return ans
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260421193010840](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260421193010840.png)



### T22161: 哈夫曼编码树

greedy, http://cs101.openjudge.cn/practice/22161/

思路：利用堆，每次弹出两个最小的节点合并，直到只剩下一个根节点。把子节点的字符都放到父节点中便于查找。 解码时，每次到达叶子节点，记录字符，回到根节点。



代码：

```python
import heapq
class Node:
    def __init__(self,val='',weight=0):
        self.val=val
        self.weight=weight
        self.left=None
        self.right=None
    def __lt__(self,other):
        if self.weight==other.weight:
            return self.val[0]<other.val[0]
        return self.weight<other.weight
heap=[]
n=int(input())
for _ in range(n):
    st,fre=input().split()
    node=Node(st,int(fre))
    heapq.heappush(heap,node)
while len(heap)>1:
    node1=heapq.heappop(heap)
    node2=heapq.heappop(heap)
    n_st=''.join(sorted(list(node1.val+node2.val)))
    n_node=Node(n_st,node1.weight+node2.weight)
    n_node.left=node1
    n_node.right=node2
    heapq.heappush(heap,n_node)
root=heap[0]
def encode(s):
    ans=''
    re=root
    for ch in s:
        while re:
            if ch in re.left.val:
                ans+='0'
                re=re.left
            elif ch in re.right.val:
                ans+='1'
                re=re.right
            if not re.left and not re.right:
                break
        re=root
    return ans
def decode(s):
    ans=''
    re=root
    for ch in s:
            if ch=='0':
                re=re.left
            elif ch=='1':
                re=re.right
            if not re.left and not re.right:
                ans+=re.val
                re=root
    return ans
while True:
    try:
        line=input()
        if line[0].isalpha():
            print(encode(line))
        elif line[0].isdigit():
            print(decode(line))
    except EOFError:
        break
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260421193136849](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260421193136849.png)



### M晴问9.5: 平衡二叉树的建立

手搓AVL, https://sunnywhy.com/sfbj/9/5/359

思路：自己确实想不到思路，复刻了一下课件中的模板



代码

```python
class Node:
    def __init__(self,value):
        self.value=value
        self.left=None
        self.right=None
        self.height=1
class AVL:
    def __init__(self):
        self.root=None
    def insert(self,value):
        self.root=self._insert(value,self.root)
    def _insert(self,value,node):
        if not node:
            return Node(value)
        elif value<node.value:
            node.left=self._insert(value,node.left)
        else:
            node.right=self._insert(value,node.right)
        node.height=1+max(self.get_height(node.left),self.get_height(node.right))
        balance=self.get_balance(node)
        if balance>1:
            if value<node.left.value:
                return self.rotate_right(node)
            else:
                node.left=self.rotate_left(node.left)
                return self.rotate_right(node)
        if balance<-1:
            if value>node.right.value:
                return self.rotate_left(node)
            else:
                node.right=self.rotate_right(node.right)
                return self.rotate_left(node)
        return node
    def get_height(self,node):
        if not node:
            return 0
        return node.height
    def get_balance(self,node):
        if not node:
            return 0
        return self.get_height(node.left)-self.get_height(node.right)
    def rotate_left(self,z):
        y=z.right
        T2=y.left
        y.left=z
        z.right=T2
        z.height=1+max(self.get_height(z.left),self.get_height(z.right))
        y.height=1+max(self.get_height(y.left),self.get_height(y.right))
        return y
    def rotate_right(self,y):
        x=y.left
        T2=x.right
        x.right=y
        y.left=T2
        y.height=1+max(self.get_height(y.left),self.get_height(y.right))
        x.height=1+max(self.get_height(x.left),self.get_height(x.right))
        return x
def pre(node):
    if node:
        l.append(node.value)
        pre(node.left)
        pre(node.right)
n=int(input())
seq=list(map(int,input().split()))
avl=AVL()
for value in seq:
    avl.insert(value)
l=[]
pre(avl.root)
print(*l)    
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260422001130561](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260422001130561.png)



### M208.实现Trie（前缀树）

trie, https://leetcode.cn/problems/implement-trie-prefix-tree/

思路：前缀树就是一个多叉树，子节点的值就是下一个字符，插入和查找时只需要一层层向下。这里为了方便查找，没有把值放在节点中，而是建立了从值到节点的映射。



代码

```python
class Node:
    def __init__(self):
        self.children={}
        self.end=False
class Trie:

    def __init__(self):
        self.root=Node()

    def insert(self, word: str) -> None:
        re=self.root
        for ch in word:
            if ch not in re.children:
                re.children[ch]=Node()
            re=re.children[ch]
        re.end=True
    def search(self, word: str) -> bool:
        re=self.root
        for ch in word:
            if ch not in re.children:
                return False
            re=re.children[ch]
        return re.end
    def startsWith(self, prefix: str) -> bool:
        re=self.root
        for ch in prefix:
            if ch not in re.children:
                return False
            re=re.children[ch]
        return True


# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260421193937185](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260421193937185.png)



### M307.区域和检索 - 数组可修改

segment tree, https://leetcode.cn/problems/range-sum-query-mutable/

思路：根据每个小区间的长度就是右端点的lowbit值的特征构建和使用树状数组。注意树状数组的索引从1开始。



代码

```python
class NumArray:
    def lowbit(self,x):
        return x&-x

    def __init__(self, nums: List[int]):
        self.n=len(nums)
        self.nums=nums
        self.tree=[0]*(self.n+1)
        for i,num in enumerate(nums,1):
            self.tree[i]+=num
            nxt=i+self.lowbit(i)
            if nxt<=self.n:
                self.tree[nxt]+=self.tree[i]
    def update(self, index: int, val: int) -> None:
        delta=val-self.nums[index]
        self.nums[index]=val
        index+=1
        while index<=self.n:
            self.tree[index]+=delta
            index+=self.lowbit(index)

    def get_pre(self,index:int) -> int:
        index+=1
        ans=0
        while index>0:
            ans+=self.tree[index]
            index-=self.lowbit(index)
        return ans

    def sumRange(self, left: int, right: int) -> int:
        return self.get_pre(right)-self.get_pre(left-1)


# Your NumArray object will be instantiated and called as such:
# obj = NumArray(nums)
# obj.update(index,val)
# param_2 = obj.sumRange(left,right)
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260421193626875](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260421193626875.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

跟随每日选做，学习了最小生成树MST，和寻找最近共同祖先LCA的算法。感觉树逐渐困难起来了，每一个模块都能学习一下午。

## 最小生成树MST

#### 建树方式

##### Kruskal 算法

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        root_x, root_y = self.find(x), self.find(y)
        if root_x == root_y:
            return False
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        return True

def kruskal(n, edges):
    # edges: (weight, u, v)
    edges.sort()
    uf = UnionFind(n)
    mst = []
    total_weight = 0

    for weight, u, v in edges:
        if uf.union(u, v):
            mst.append((u, v, weight))
            total_weight += weight
            if len(mst) == n - 1:
                break
    return mst, total_weight
```

##### Prim算法

```python
import heapq

def prim(n, graph):
    # graph: 邻接表，graph[u] = [(v, weight), ...]
    visited = [False] * n
    min_heap = [(0, 0, -1)]  # (weight, current_node, parent)
    mst = []
    total_weight = 0

    while min_heap and len(mst) < n:
        weight, u, parent = heapq.heappop(min_heap)
        if visited[u]:
            continue
        visited[u] = True
        if parent != -1:
            mst.append((parent, u, weight))
            total_weight += weight
        for v, w in graph[u]:
            if not visited[v]:
                heapq.heappush(min_heap, (w, v, u))
    return mst, total_weight
```

#### 不平等边的问题

对于 OJ30313：0-W最小生成树 和 LC3600.升级后最大生成树的稳定性 这类问题

他们都存在两类边，第一类因为没有权值或者必须选用所以可以直接连接，第二类是需要做出最优选择的边。

思路是：

0.这类题一般需要使用并查集辅助判环，统计编号，即使用Kruskal算法

1.连接第一类边，然后把每个连接好的模块视为一个整体，对他们重新编号

2.对这些新的集合节点使用Kruskal算法建树

以0-W最小生成树为例，LC的题只需在此基础加判环和判断是否成树

```python
class UnionFind:
    def __init__(self, N):
        self.parent = list(range(N))
        self.rank = [0] * N
        self.n=N

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        root_x, root_y = self.find(x), self.find(y)
        if root_x == root_y:
            return False
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        return True
    def count(self):  #统计新的编号
        switch_id={}
        count=0
        for i in range(self.n):
            root=self.find(i)
            if root not in switch_id:
                switch_id[root]=count
                count+=1
        return switch_id,count

from collections import deque,defaultdict
n,m=map(int,input().split())
uf1=UnionFind(n)
edges=[]
assign=defaultdict(set)
for _ in range(m):
    u,v,w=map(int,input().split())
    edges.append((w,u-1,v-1))
    assign[u-1].add(v-1)
    assign[v-1].add(u-1)
unvisited=set(range(n))
while unvisited: #连接无权边
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
edges.sort()
total_weight=0
for w,u,v in edges: #连接有权边
    c_u=com_id[uf1.find(u)]
    c_v=com_id[uf1.find(v)]
    if uf2.union(c_u,c_v):
        total_weight+=w
        com_count-=1
        if com_count==1:
            break
print(total_weight)
```

判环

```python
for s,u,v in edges1:
        if uf1.union(u,v):
            ans=min(ans,s)
        else:
            return -1
```

## LCA(最近共同祖先)

#### RMQ（最快）

根据定理，用dfs遍历的树，遍历序在两个节点之间的最浅父节点就是他们的最近共同祖先，最浅父节点的dn值最小。

因此，我们需要记录每个节点的遍历序dn ，st[k] [j]代表遍历序起点为j，长度为2**k的区间中最浅父节点。先把dfs遍历所有节点，赋予dn值，记录直接父节点。然后动态规划推导出st表。查找时，计算区间长度，把区间分为两部分找最小值。

为了避免在最近共同祖先是某个节点本身是错误的输出了他的父节点，区间左端点需要+1

```python
import sys
def main():
    data = list(map(int, sys.stdin.read().split()))
    idx = 0
    N = data[idx]; idx += 1
    M = data[idx]; idx += 1
    S = data[idx]; idx += 1
    num = 5 * 10**5 + 1
    graph = [[] for _ in range(num)]
    for _ in range(N-1):
        x = data[idx]
        y = data[idx+1]
        graph[x].append(y)
        graph[y].append(x)
        idx += 2
    LOG = 19
    dfn = [0] * num
    st = [[0]*(N+1) for _ in range(LOG)]
    dn = 0
    stack = [(S, 0)]
    vis = [False] * num
    while stack:
        node, father = stack.pop()
        if vis[node]:
            continue
        vis[node] = True
        dn += 1
        dfn[node] = dn
        st[0][dn] = father
        for child in reversed(graph[node]):
            if child != father and not vis[child]:
                stack.append((child, node))
    def get(m, n):
        return m if dfn[m] < dfn[n] else n
    for k in range(1, LOG):
        for j in range(1, N - (1 << k) + 2):
            st[k][j] = get(st[k-1][j], st[k-1][j + (1 << (k-1))])
    def lca(a, b):
        if a == b:
            return a
        a, b = dfn[a], dfn[b]
        if a > b:
            a, b = b, a
        a += 1
        l = b - a + 1
        d = l.bit_length() - 1
        return get(st[d][a], st[d][b - (1 << d) + 1])
    output = []
    for _ in range(M):
        output.append(str(lca(data[idx], data[idx+1])))
        idx += 2
    print('\n'.join(output))
if __name__ == "__main__":
    main()
```

#### 倍增法

遍历时记录所有节点的深度，up[k] [u] 代表节点u向上走2**k步到达的节点。用动态规划推导出up表。查找时，把深度的的节点向上跳，直到与另一个节点深度相同，再把两个节点共同跳到深度最小的不同节点。这个节点的父节点就是最近共同祖先。

为了避免再最近共同祖先时某个节点本身时错误的输出了他的父节点，当两个节点位于同一高度时，如果两个节点时同一节点，直接返回

```python
import sys
def main():
    data = list(map(int, sys.stdin.read().split()))
    idx = 0
    N = data[idx]; idx += 1
    M = data[idx]; idx += 1
    S = data[idx]; idx += 1
    LOG = 19
    graph = [[] for _ in range(N + 1)]
    for _ in range(N - 1):
        u = data[idx]
        v = data[idx+1]
        graph[u].append(v)
        graph[v].append(u)
        idx += 2
    up = [[0]*(N+1) for _ in range(LOG)]
    depth = [0]*(N+1)
    stack = [(S, 0)]
    while stack:
        u, fa = stack.pop()
        up[0][u] = fa
        for v in graph[u]:
            if v != fa:
                depth[v] = depth[u] + 1
                stack.append((v, u))
    for k in range(1, LOG):
        for u in range(1, N+1):
            up[k][u] = up[k-1][ up[k-1][u] ]
    def lca(u, v):
        if depth[u] < depth[v]:
            u, v = v, u
        for k in range(LOG-1, -1, -1):
            if depth[u] - (1 << k) >= depth[v]:
                u = up[k][u]
        if u == v:
            return u
        for k in range(LOG-1, -1, -1):
            if up[k][u] != up[k][v]:
                u = up[k][u]
                v = up[k][v]
        return up[0][u]
    out = []
    for _ in range(M):
        u = data[idx]
        v = data[idx+1]
        out.append(str(lca(u, v)))
        idx += 2
    print('\n'.join(out))
if __name__ == "__main__":
    main()
```

##### 优化

可以看出两个算法虽然底层逻辑不同，但在形式上相似度很高。使用以下三个共同的优化思路：

1）由于python对二维列表的缓存方式是一行一行存，所以遍历时把先固定行，再查询列会比反过来更快，而我们每次都是先固定k，因此把k设为行。

2）用栈模拟的dfs比用递归进行dfs更快。

3）数据一次性读取，一次性输出，把主程序用main()函数封装。
