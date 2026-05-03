## 二叉树

#### 建树

二叉树的每个节点只有左右两个子节点

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

#### 遍历

前中后序**dfs**

```python
def inorderTraversal(root):
    l=[]
    def dfs(lo):
        if lo:
            dfs(lo.left)
            l.append(lo.val)
            dfs(lo.right)
    dfs(root)
    return l
```

> 前序和后序也同理，只是需要改变函数中三行的顺序

层序**bfs**

```python
def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
    from collections import deque
    queue=deque([(root,0)])
    ans=[]
    while queue:
        lo,layer=queue.popleft()
        if lo:
            if len(ans)<layer+1:
                ans.append([])
            ans[layer].append(lo.val)
            queue.append((lo.left,layer+1))
            queue.append((lo.right,layer+1))
    return ans        
```

> bfs也是遍历树的常规手段，如LC222.完全二叉树的节点个数

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def countNodes(self, root: Optional[TreeNode]) -> int:
        stack=[root]
        ans=0
        while stack:
            node=stack.pop()
            if node:
                ans+=1
                stack.append(node.left)
                stack.append(node.right)
        return ans
```

#### 二叉搜索树BST

对二叉搜索树的每个节点而言，它左子节点比它小，右子节点比它大

##### 从有序数组建树（平衡二叉树）

```python
def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
    if not nums:
        return None
    mid=len(nums)//2
    root=TreeNode(nums[mid])
    root.left=self.sortedArrayToBST(nums[:mid])
    root.right=self.sortedArrayToBST(nums[mid+1:])
    return root
```

##### 从无序数组建树（不平衡）

```python
class TreeNode:
    def __init__(self,val=0,left=None,right=None):
        self.val=val
        self.left=left
        self.right=right
def insert(node,num):
    if not node:
        return TreeNode(num)
    if node.val>num:
        node.left=insert(node.left,num)
    elif node.val<num:
        node.right=insert(node.right,num)
    return node
nums=list(map(int,input().split()))
root=None
for n in nums:
    root=insert(root,n)
```

##### 查找第K小的元素

二叉搜索树的中序遍历就是正序的序列。可以直接用dfs中序遍历，按照索引查询，但这样需要遍历完整个树，时间长。

使用中序遍历的迭代写法可以在找到时就终止遍历，缩短时间。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        i=1
        stack=[]
        while stack or root:
            while root:
                stack.append(root)
                root=root.left
            node=stack.pop()
            if i==k:
                return node.val
            i+=1
            root=node.right
```



#### 哈夫曼编码树

哈夫曼编码树是一种二叉树，根据字符使用频率(权值)生成，能根据字符出现频率为字符串编码，使总的编码长度最小。

##### 规则

确保哈夫曼编码树唯一性的生成规则是：

选取最小的两个节点合并时，节点比大小的规则是:

1) 权值小的节点算小。权值相同的两个节点，字符集里最小字符小的，算小。例如 （{'c','k'},12) 和 ({'b','z'},12)，后者小。
2) 合并两个节点时，小的节点必须作为左子节点
3) 连接左子节点的边代表0,连接右子节点的边代表1

节点形式：每个节点都有变量和权值。叶子节点的变量是一个字母，权值是这个字母的权值。其余的各级根节点的权值是所有子节点的权值的总和，变量是一个子节点字母组成的有序字符串。

##### 生成

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
```

##### 编码（字符串->01串）

```python
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
```

##### 解码（01串->字符串）

```python
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
```



#### 树形DP

例1：OJ24637：宝藏二叉树（如果两个节点之间有边，那么最多只能取其中一个节点）

由于是完全二叉树，在层序遍历中，根节点i和左右子节点2i+1和2i+2存在索引联系，因此可以用dp1和dp2两个数组表示每个节点及其子树的最大值。

```python
N=int(input())
t=list(map(int,input().split()))
dp1=[0]*N #使用子树根节点
dp2=[0]*N #不使用子树根节点
for i in range(N-1,-1,-1):
    if 2*i+2<N:
        dp1[i]=t[i]+dp2[2*i+1]+dp2[2*i+2]
        dp2[i]=max(dp2[2*i+1]+dp1[2*i+2],dp1[2*i+1]+dp2[2*i+2],dp1[i*2+1]+dp1[i*2+2],dp2[i*2+1]+dp2[i*2+2])
    elif 2*i+1<N:
        dp1[i]=t[i]
        dp2[i]=dp1[2*i+1]
    else:
        dp1[i]=t[i]
print(max(dp1[0],dp2[0]))
```

##### 多叉树的树形dp

遍历树，记录所有节点的深度，推导dp表时从深向浅计算，保证先计算子节点再计算父节点。

dp1记录使用当前节点的最大值，dp2记录不使用当前节点呃最大值。遍历子节点统计dp1对应的子节点最大值c1和dp2对应的子节点最大值c2。c1只可以从dp2中取值，c2可以从dp1和dp2中取值。因为r值有负数，所以取值时如果是负数就不取（表示为+0）

```python
n=int(input())
r=[0]+list(map(int,[input() for _ in range(n)]))
graph=[[] for _ in range(n+1)]
depth=[]
father=[0]*(n+1)
for _ in range(n-1):
    l,k=map(int,input().split())
    father[l]=k
    graph[k].append(l)
root=0
for t in range(1,n+1):
    if father[t]==0:
        root=t
        break
stack=[(root,0)]
while stack:
    node,dep=stack.pop()
    if len(depth)<dep+1:
        depth.append([])
    depth[dep].append(node)
    for child in graph[node]:
        stack.append((child,dep+1))
dp1=[float('-inf')]*(n+1)
dp2=[float('-inf')]*(n+1)
for nodes in depth[::-1]:
    for node in nodes:
        c1=0
        c2=0
        for child in graph[node]:
            c1+=max(0,dp2[child])
            c2+=max(0,dp1[child],dp2[child])
        dp1[node]=r[node]+c1
        dp2[node]=c2
print(max(dp1[root],dp2[root]))
```

特例：CF2195E-Idiot First Search（遍历式dp）

到达某节点，如果想进入其父节点，需要将这个节点的子节点全都遍历一遍。因此节点1到节点0的步数是最少的，只需要遍历所有节点。而其他节点的步数都是遍历自身所有子节点的步数加其父节点到节点0的步数加1。只需统计每个节点的父节点，和下面的所有子节点数（包括子节点的子节点）。状态转移方程：dp[i]=(dp[parent[i]]+1+(2*child_num[i]))

统计父节点很容易。统计子节点，需要等整棵树都构建好，自底向上计算。先对树进行层序遍历，再根据层序遍历结果的倒序，按方程计算。child_num[i]=child_num[nodes[i].left.val]+child_num[nodes[i].right.val]+2

计算步数时，根据层序遍历顺序，保证计算到某个节点时，其父节点已经被计算完毕。

```python
from collections import deque
class TreeNode:
    def __init__(self,val):
        self.val=val
        self.left=None
        self.right=None
def layer(node):
    queue=deque([node])
    ans=[]
    while queue:
        re=queue.popleft()
        ans.append(re.val)
        if re.left:
            queue.append(re.left)
        if re.right:
            queue.append(re.right)
    return ans
t=int(input())
for _ in range(t):
    n=int(input())
    nodes=[TreeNode(x) for x in range(n+1)]
    parent=[0]*(n+1)
    child_num=[0]*(n+1)
    for i in range(1,n+1):
        l,r=map(int,input().split())
        if l==r==0:
            continue
        parent[l]=parent[r]=i
        nodes[i].left=nodes[l]
        nodes[i].right=nodes[r]
    seq=layer(nodes[1])
    for i in seq[::-1]:
        if nodes[i].left:
            child_num[i]=child_num[nodes[i].left.val]+child_num[nodes[i].right.val]+2
    dp=[0]*(n+1)
    for i in seq:
        dp[i]=(dp[parent[i]]+1+(2*child_num[i]))%(10**9+7)
    print(*dp[1:])
```

## 最小生成树MST

#### 建树方式

##### Kruskal 算法（并查集）

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
            return False #判断是否已经连通
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

##### Prim算法（堆）

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



## 多叉树

将二叉树的左右子节点改为children列表或字典。

多叉树也可以用bfs和dfs进行遍历，当给出遍历结果时，也可以反向用于建树

如OJ07161：森林的带度数层次序列存储（先用bfs建树，然后dfs后序遍历）

```python
class TreeNode:
    def __init__(self,val=None,idx=0):
        self.idx=idx
        self.val=val
        self.children=[]
from collections import deque
ans=[]
def back_order(loc):
    for ch in loc.children:
        back_order(ch)
    ans.append(loc.val)
n=int(input())
for _ in range(n):
    l=list(input().split())
    tree=[]
    for i in range(0,len(l),2):
        tree.append((l[i],int(l[i+1])))
    root=TreeNode(tree[0][0],0)
    queue=deque([root])
    index=0
    while queue:
        lo=queue.popleft()
        for _ in range(tree[lo.idx][1]):
            index+=1
            new=TreeNode(tree[index][0],index)
            lo.children.append(new)
            queue.append(new)
    back_order(root)
print(*ans)
```

## 括号嵌套树

括号嵌套树是一个用括号体现节点间的关系的字符串，其中二叉树会有*表示的空节点，遍历时需要去掉。

#### 前序遍历

括号嵌套树的前序遍历，就是字符串中节点值的顺序

#### 中序遍历

只有二叉树能这样遍历  OJ27637:括号嵌套二叉树

```python
n=int(input())
for _ in range(n):
    pre = []
    stack = []
    for ch in input():
        if ch==')': #二叉树已知子节点数目，所以直接pop()
            r=stack.pop()
            stack.pop()
            l=stack.pop()
            stack.pop()
            root=stack.pop()
            stack.append(l+root+r)
        else:
            stack.append(ch)
            if ch not in ['(',',']:
                pre.append(ch)
    pres=''.join(pre).replace('*','')
    mid=''.join(stack).replace('*','')
    print(pres)
    print(mid)
```

#### 后序遍历

多叉树和二叉树都可以这样遍历  OJ24729:括号嵌套树

```python
pre=[]
stack=[]
for ch in input():
    if ch==')':
        t=''
        while stack: #多叉树不知道子节点数目，所以需要while循环
            re=stack.pop()
            if re=='(':
                t=t+stack.pop()
                break
            if re!=',':
                t=re+t
        stack.append(t)
    else:
        if ch!=',':
            stack.append(ch)
            if ch!='(':
                pre.append(ch)
print(''.join(pre))
print(''.join(stack))
```



## 位运算

移位（<< & >>）是很实用的操作，能更快的放缩数字

移位可以更快获得一个大数，如1<<k表示2**k

左移<<能将数字放大2的指定幂次，应用在从小到大查询，二进制数拼接等

LC3827.统计单比特整数

```python
class Solution:
    def countMonobit(self, n: int) -> int:
        ans=1;pows=1
        while pows<=n:
            ans+=1
            pows=((pows<<1)+1) #直接跳到下一个单比特整数
        return ans
```

LC1680连接连续二进制数字

```python
class Solution:
    def concatenatedBinary(self, n: int) -> int:
        ans,bit,pows=0,1,2
        for i in range(1,n+1):
            if i==pows:
                bit+=1
                pows*=2
            ans=((ans<<bit)+i)%(10**9+7) 
            #将原数字左移下一个数字的位数，留出适当的空间
        return ans
```

右移>>将数字缩小2的指定幂次，也有些题目将二进制数当作01字符串看待，移位相当于遍历字符串

LC1404.将二进制表示减到1的步骤数

```python
class Solution:
    def numSteps(self, s: str) -> int:
        n=int(s,2)
        step=0
        while n!=1:
            step+=1
            if n%2==1:
                n+=1
            else:
                n>>=1 #将数字缩小2倍
        return step
```

LC868.二进制间距

```python
class Solution:
    def binaryGap(self, n: int) -> int:
        ans=0
        last_i=-1
        i=0
        while n!=0:
            bit=n&1 #取得末位数字
            n>>=1 #遍历下一位
            if bit==1:
                if last_i!=-1:
                    ans=max(ans,i-last_i)
                last_i=i
            i+=1
        return ans
```

运用左移和右移，可以颠倒二进制数

LC190.颠倒二进制位

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        ans=0
        for _ in range(32):
            bit=n&1 #取得末位数字
            n>>=1
            ans=(ans<<1)+bit
        return ans
```

> n&1 是常用的取得末位数字的方法

一些有关位运算的内置函数

int(s,2)将01字符串转为对应的十进制数

x.bit_count().统计二进制表示的x中‘1’的数量

f'{n:0xxb}'规定二进制数的位数为xx，不足的用0补全

## Manacher算法（寻找回文串）

找出字符串中的子回文串效率最高的Manacher算法

```python
class Solution:
    def countSubstrings(self, s: str) -> int:
        s1='#'.join('^'+s+'&')
        halfl=[0]*(len(s1)-2)
        halfl[1]=1
        ans=0
        c,r=0,0
        for i in range(2,len(s1)-2):
            l=1
            if i<=r:
                l=min(halfl[(2*c)-i],r-i+1)
            while s1[i-l]==s1[i+l]:
                l+=1
                c=i
                r=i+l-1
            halfl[i]=l
            ans+=l//2
        return ans 
```

这个算法得到的是以每个字符（或空位）为中心的最大回文半径（的二倍）

若想转换为判断某个区间的字符是否是回文串，只需这个函数（区间左闭右开）

```python
def switch(a,b): #a,b是未添加‘#’前的原字符串的索引
    return halfl[a+b+1]>=(b-a)
```

若只需要判断整体字符串是否回文，deque是个好选择

```python
from collections import deque
def pal(s):
    n=deque(list(s))
    while len(n)>1:
        if n.popleft()!=n.pop():
            return False
    return True
```

## 逆序对统计

#### merge sort

```python
count=[0]
def merge_sort(nums):
    if len(nums)==1:
        return nums
    mid=len(nums)//2
    left=merge_sort(nums[:mid])
    right=merge_sort(nums[mid:])
    return merge(left,right)
def merge(a,b):
    ans=[]
    i,j=0,0
    while i<len(a) and j<len(b):
        if a[i]>b[j]:
            ans.append(b[j])
            count[0]+=len(a)-i
            j+=1
        else:
            ans.append(a[i])
            i+=1
    ans.extend(a[i:] or b[j:])
    return ans
```

#### binary indexed tree

```python
class BinaryIndexedTree:
    def __init__(self,m):
        self.m=m
        self.tree=[0]*(m+1)
    def update(self,index,num):
        i=index
        while i<self.m:
            self.tree[i]+=num
            i+=i&-i
    def query(self,index):
        i=index
        ans=0
        while i>0:
            ans+=self.tree[i]
            i-=i&-i
        return ans
count=0
t=BinaryIndexedTree(len(arr))
for x in range(len(arr)-1,-1,-1):
    count+=t.query(arr[x]-1)
    t.update(arr[x],1)
```

## 状态压缩 

**用二进制字符串的0代表空位，1代表已存在，当全为1就是一个满位。但不用字符串的方式存储，而是以数字的形式，并用位运算进行状态改变和状态查询。**

LC37.解数独 用状态压缩存储每行、每列、每个3*3矩阵中已有的1-9中的数字

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        set_h=[0]*9
        set_s=[0]*9
        set_f=[[0]*3 for _ in range(3)]
        for i in range(9):
            for j in range(9):
                num=board[i][j]
                if num!='.':
                    num=int(num)-1
                    set_h[i]|=(1<<num)
                    set_s[j]|=(1<<num)
                    set_f[i//3][j//3]|=(1<<num)
        def get_best():
            m,n=-1,-1
            mi=10
            for i in range(9):
                for j in range(9):
                    if board[i][j]=='.':
                        re=9-(set_h[i]|set_s[j]|set_f[i//3][j//3]).bit_count()
                        if re<mi:
                            mi=re
                            m,n=i,j
                            if re==1:
                                return m,n
            return m,n
        def dfs():
            i,j=get_best()
            if i==j==-1:
                return True
            mask=set_h[i]|set_s[j]|set_f[i//3][j//3]
            for c in range(9):
                m=1<<c
                if not m&mask:
                    board[i][j]=str(c+1)
                    set_h[i]|=m
                    set_s[j]|=m
                    set_f[i//3][j//3]|=m
                    if dfs():
                        return True
                    set_h[i]^=m
                    set_s[j]^=m
                    set_f[i//3][j//3]^=m
                    board[i][j]='.'
            return False
        dfs()
```

OJ30201：旅行商售货问题 用状态压缩存储已经到达过的城市

```python
def s_c_dp(n,cost):
    dp=[[float('inf')]*n for _ in range(1<<n)]
    for i in range(n):
        dp[1<<i][i]=0
    for mask in range(1<<n):
        for i in range(n):
            if not (mask&(1<<i)) or dp[mask][i]==float('inf'):
                continue
        for j in range(n):
            if mask&(1<<j):
                continue
            new_mask=mask|(1<<j)
            dp[new_mask][j]=min(dp[new_mask][i],dp[mask][i]+cost[i][j])
    result=floot('inf')
    full_mask=(1<<n)-1
    for i in range(n):
        result=min(result,dp[full_mask][i]+结束代价)
    return result
```

## KMP算法（字符串匹配）

KMP的原理是当前字符不匹配时，不放弃前面匹配好的结果。

因为记录了最长前缀后缀，所以对于一段与主串匹配好的后缀，前面一定有个完全相同的前缀。

下次尝试时,假定主串不移动，将模式串整体右移，将前缀放在后缀的位置，这样就能使匹配更加高效。

KMP其实只有这一小段代码，与主串匹配的代码其实完全同理，只不过被匹配的对象不是自身而是主串。

```python
n = len(pattern)
    lps = [0] * n
    length = 0
    for i in range(1, n): 
        while length > 0 and pattern[i] != pattern[length]:
            length = lps[length - 1]
        if pattern[i] == pattern[length]:
            length += 1
        lps[i] = length
```

KMP的拓展

引理：最小循环节 假设一个字符串的长度为 L，其对应的 LPS 数组最后一位为 lps[L-1]。 若满足 

L  % ( L − lps[L−1] ) == 0 且 lps[L-1] != 0 (若lps[L-1]==0说明字符串没有循环节)

则该字符串由一个长度为 L - lps[L-1] 的子串重复构成。该子串即为最小循环元，重复次数 K = L / d。

直观理解：ababababab

​		    ^^^^^^^^      前缀

​		        ^^^^^^^^  后缀      lps[L-1]=8

​	            ^^                   L-lps[L-1]=2

```python
 d=n-lps[n-1]
    if n%d==0 and n!=d:
        return n//d
    else:
        return 1 #这两句本质上能合并，在此为了是区分字符串有或没有循环节的情况
```

## 二分查找

#### 基础版

```python
def searchInsert(self, nums: List[int], target: int) -> int:
    lo,hi=0,len(nums)
    while lo<hi:
        mid=(lo+hi)//2
        if nums[mid]>=target:
            hi=mid
        else:
            lo=mid+1
    return hi
```

这是一个左闭右开区间，hi可以等于大于目标的数，但lo要比小于目标的数至少大1。最终答案是hi，因此与目标相等的情况也并入hi。这样当目标不存在时，hi就代表插入位置。

#### 应用

OJ08210：河中跳房子 http://cs101.openjudge.cn/pctbook/M08210

```python
L,N,M=map(int,input().split())
rocks=[int(input()) for _ in range(N)]
rocks.append(L)
def check(x): #用于判断的函数，不同的题目依据题意写判断函数
    l,c=0,0
    for num in rocks:
        if num-l<x:
            c+=1
        else:
            l=num
    return c<=M
lo,hi=1,L+1
while lo<hi:
    mid=(lo+hi)//2
    if check(mid):
        lo=mid+1 #这里为了让区间最终能接合，让左区间比答案大1
    else:
        hi=mid #这里记录的是恰好不行的数字
print(hi-1) #因此答案是hi-1
```

由此可见，其实使用左闭右开区间，并且把答案记录在hi中，是可以万能的。二分只要记住一种方式就可以，并不需要为了题目改变，可以调整的是hi的使用方式，比如输出hi-1。

#### 特殊情况下比二分快的搜索方式

LC240.搜索二维矩阵II https://leetcode.cn/problems/search-a-2d-matrix-ii/

每次查找矩阵最右上角的数字。如果比目标大，说明矩阵的最右边一列都比目标大，直接去掉这一列。如果比目标小，说明矩阵最上面一行都比目标小，去掉这一行。

```python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        m,n=len(matrix),len(matrix[0])
        r,c=0,n-1
        while r<m and c>=0:
            if matrix[r][c]==target:
                return True
            elif matrix[r][c]>target:
                c-=1
            else:
                r+=1
        return False
```

这里运用了矩阵中右边和下面的数字都比当前数字大的性质，使时间复杂度来到了O(m+n)

## 链表

#### 基础结构

```python
class ListNode:
	def __init__(self, x):
		self.val = x
		self.next = None
```

#### 快慢指针

**应用1**：找到无环链表的中点（偶数节点数链表的中点右侧）

```python
slow,fast=head,head
while fast and fast.next:
    slow=slow.next
    fast=fast.next.next
```

**应用2**：判断链表是否有环（有环的链表快慢指针会相交）

判断环

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        slow,fast=head,head
        while fast and fast.next:
            slow=slow.next
            fast=fast.next.next
            if slow==fast:
                return True
        return False
```

进阶：找到入环节点

原理：设头节点到入环点的距离为a，环的长度为c。 

快慢指针相遇时，步数fast-步数slow=n×c，且步数fast=步数slow×2，故步数slow=n×c。 

此时把re放在头节点，re和slow同步移动，re移动a步，来到入环节点时，slow一共移动了a+n×c步，也来到入环节点。

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow,fast=head,head
        flag=False
        while fast and fast.next:
            slow=slow.next
            fast=fast.next.next
            if slow is fast:
                flag=True
                break
        if flag:
            re=head
            while re and slow:
                if re is slow:
                    break
                re=re.next
                slow=slow.next
        if flag:
            return re
        else:
            return None
```

#### 哨兵节点

合并两个有序链表

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        a,b=list1,list2
        ans=h=ListNode() #ans就是哨兵节点，放在答案的前面，h是可移动的节点
        while a and b:
            if a.val<=b.val:
                h.next=a
                a=a.next
            else:
                h.next=b
                b=b.next
            h=h.next
        h.next=a or b
        return ans.next #ans的后面连着答案
```

双向链表，实现对链表头尾都能直接查询到

```python
class Node:
    def __init__(self,val=0,key=0,last=None,next=None):
        self.val=val
        self.key=key
        self.last=last
        self.next=next
class LRUCache:
	def __init__(self, capacity: int):
        self.c=capacity
        self.sen=Node()
        self.sen.last=self.sen
        self.sen.next=self.sen
```

#### 常用操作

反转操作

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        h=head
        re=None
        while h:
            nxt=h.next
            h.next=re
            re=h
            h=nxt
        return re
```

删节点操作（单向链表只需第一句）

```python
def delete(self,node):
        node.next.last=node.last
        node.last.next=node.next
```

两两交换操作

```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        b=a=ListNode() #b是哨兵节点
        a.next=head
        while a.next and a.next.next:
            re=a.next
            a.next=a.next.next
            a=a.next
            nxt=a.next
            a.next=re
            a=a.next
            a.next=nxt
        return b.next    
```

## 调度场算法

本质是把不同类型的信息分开放置，例如把字符信息和数字信息分开。学会这个，所有的字符串解码或者换序问题就都同理了。

中序表达式转后序表达式

```python
n=int(input())
seq={'+':1,'-':1,'*':2,'/':2}
for _ in range(n):
    s=input()
    stack=[] #存储字符
    ans=[] #存储数字和确定的运算符
    num='' #数字长度未知，用于把分开的字符合并成一个数字
    for ch in s:
        if ch.isdigit() or ch=='.': #数字整合
            num+=ch
        else:
            if num: #数字存储
                ans.append(num)
                num=''
            if ch==')': #处理括号圈出的优先区域
                while stack and stack[-1]!='(':
                    ans.append(stack.pop())
                stack.pop() #优先区域读取结束
            elif ch=='(': 
                stack.append(ch)
            else: #题目特异性操作，涉及运算符顺序
                while stack and stack[-1] in '+-*/' and seq[stack[-1]]>=seq[ch]:
                    ans.append(stack.pop())
                stack.append(ch)
    if num:
        ans.append(num)
    while stack:
        ans.append(stack.pop())
    print(' '.join(ans))
```

字符解码

```python
class Solution:
    def decodeString(self, s: str) -> str:
        nums=[] #存储数字
        stack=[] #存储字符
        num='' #数字长度未知，用于把分开的字符
        for ch in s:
            if ch.isdigit():
                num+=ch
            else:
                if num: #数字存储
                    nums.append(int(num))
                    num=''
                if ch==']': #处理括号圈出的优先区域
                    re=''
                    while stack and stack[-1]!='[':
                        re=stack.pop()+re
                    stack.pop()  #优先区域读取结束
                    mult=nums.pop() #题目特异性操作，涉及字符串加倍
                    stack.append(mult*re)
                else:
                    stack.append(ch)
        return ''.join(stack)
```

由此可见，两个题目只有题目特异性操作不同，其他可以用完全相同的方法来做。

## 单调队列/栈

队列和栈中往往储存的是索引而非具体的数字，这样方便各种定位操作。

这是一种便于查询某区间最大/最小值的数据结构，其单增（减）性有时也会有一定作用。

其单调性的维护方式是，对于当前的数字，把前面不符合单调性的数字都pop出去，形成保证含有当前元素的单调序列。

#### 一个单调栈

OJ27205：护林员盖房子又来了

单调递增栈，计算最大全0矩形面积。可以把它看作m个高度数组，按行依次计算。

核心就是当栈单调递增时，左边的高度都可以向右边延伸。

```python
m,n=map(int,input().split())
forest=[list(map(int,input().split())) for _ in range(m)]
height=[0]*n
ans=0
for i in range(m):
    height=[h+1 if loc==0 else 0 for h,loc in zip(height,forest[i])]
    stack=[]
    for j in range(n+1):
        he=height[j] if j<n else -1
        while stack and height[stack[-1]]>he:
            h=height[stack.pop()]
            w=j-stack[-1]-1 if stack else j
            ans=max(ans,h*w)
        stack.append(j)
print(ans)
```

LC239.滑动窗口最大值

这里虽然有两个队列，但只有一个是单调递减队列，另一个仅仅是辅助队列，维持两个队列长度相等。

核心是单减队列使得滑动窗口内最大的数永远在最左边。辅助队列用于窗口滑动。

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        from collections import deque
        queue=deque()
        l=deque()
        for num in nums[:k]:
            re=0
            while queue and queue[-1]<num:
                queue.pop()
                re+=(l.pop()+1)
            queue.append(num)
            l.append(re)
        ans=[queue[0]]
        for num in nums[k:]:
            re=0
            while queue and queue[-1]<num:
                queue.pop()
                re+=(l.pop()+1)
            queue.append(num)
            l.append(re)
            if l[0]>0:
                l[0]-=1
            else:
                l.popleft()
                queue.popleft()
            ans.append(queue[0])
        return ans
```



#### 两个单调队列/栈

**一般是一个单增，一个单减。**两个单调栈能解决更复杂的问题

OJ30102：完美交易窗口

用两个单调栈，一个单减，一个严格单增。每次以当前值为卖出点。单减栈的末端是前面第一个更大的数字l，严格单增栈中l后面的第一个索引就是这个区间的最小值。这里体现了栈中存放索引的重要性。

```python
import sys,bisect
data=list(map(int,sys.stdin.read().split()))
n=data[0]
mx_stack=[]
mi_stack=[]
ans=0
for i,num in enumerate(data[1:]):
    while mx_stack and data[mx_stack[-1]+1]<num:
        mx_stack.pop()
    l=mx_stack[-1] if mx_stack else -1
    while mi_stack and data[mi_stack[-1]+1]>=num:
        mi_stack.pop()
    if mi_stack:
        real_l=bisect.bisect_right(mi_stack,l)
        if real_l<len(mi_stack):
            j=mi_stack[real_l]
            ans=max(ans,i-j+1)
    mx_stack.append(i)
    mi_stack.append(i)
print(ans)
```

LGP2698.Flowerpot S

维护y值的单增队列和单减队列。每次以当前位置为右边界，寻找符合要求的左边界。因为左边界可能更大也可能更小，因此需要两个队列。

```python
from collections import deque
N,D=map(int,input().split())
drop=[]
for _ in range(N):
    drop.append(tuple(map(int,input().split())))
drop.sort()
mi_que=deque()
mx_que=deque()
ans=float('inf')
for i,loi in enumerate(drop):
    xi,yi=loi
    while mi_que and yi<=drop[mi_que[-1]][1]:
        mi_que.pop()
    mi_que.append(i)
    while mx_que and yi>=drop[mx_que[-1]][1]:
        mx_que.pop()
    mx_que.append(i)
    while mi_que and yi-drop[mi_que[0]][1]>=D:
        ans=min(ans,xi-drop[mi_que[0]][0])
        mi_que.popleft()
    while mx_que and drop[mx_que[0]][1]-yi>=D:
        ans=min(ans,xi-drop[mx_que[0]][0])
        mx_que.popleft()
print(ans if ans!=float('inf') else -1)
```

## DP

环形打家劫舍

LC3892.产生至少K个峰值的最小操作次数

```python
class Solution:
    def solve(self,a,k):
        n=len(a)
        dp=[[0]*n for _ in range(k+1)]
        for i in range(1,k+1):
            dp[i][0]=dp[i][1]=float('inf')
        ops=[max(max(a[i-1],a[i+1])-a[i]+1,0) for i in range(1,n-1)]
        for left in range(1,k+1):
            for i in range(1,n-1):
                not_choose=dp[left][i]
                choose=dp[left-1][i-1]+ops[i-1]
                dp[left][i+1]=min(not_choose,choose)
        return dp[k][n-1]
    def minOperations(self, nums: list[int], k: int) -> int:
        n=len(nums)
        if k>n//2:
            return -1
        cnt=0
        for i in range(n):
            if nums[i-1]<nums[i]>nums[(i+1)%n]:
                cnt+=1
        if cnt>=k:
            return 0
        ans1=self.solve([nums[-1]]+nums,k)
        ans2=self.solve(nums+[nums[0]],k)
        return min(ans1,ans2)
```

可以跳过两次的土豪购物

LC3418.机器人可以获得的最大金币数

因为可以“感化”两次，因此需要三个dp数组，代表“感化”0次，1次，2次的最大金币数。

```python
class Solution:
    def maximumAmount(self, coins: List[List[int]]) -> int:
        r,c=len(coins),len(coins[0])
        dp0=[[float('-inf')]*c for _ in range(r)]
        dp1=[[float('-inf')]*c for _ in range(r)]
        dp2=[[float('-inf')]*c for _ in range(r)]
        if coins[0][0]<0:
            dp0[0][0]=coins[0][0]
            dp1[0][0]=dp2[0][0]=0
        else:
            dp0[0][0]=dp1[0][0]=dp2[0][0]=coins[0][0]
        for i in range(1,r):
            dp0[i][0]=dp0[i-1][0]+coins[i][0]
            if coins[i][0]<0:
                dp1[i][0]=max(dp1[i-1][0]+coins[i][0],dp0[i-1][0])
                dp2[i][0]=max(dp2[i-1][0]+coins[i][0],dp1[i-1][0])
            else:
                dp1[i][0]=dp1[i-1][0]+coins[i][0]
                dp2[i][0]=dp2[i-1][0]+coins[i][0]
        for j in range(1,c):
            dp0[0][j]=dp0[0][j-1]+coins[0][j]
            if coins[0][j]<0:
                dp1[0][j]=max(dp1[0][j-1]+coins[0][j],dp0[0][j-1])
                dp2[0][j]=max(dp2[0][j-1]+coins[0][j],dp1[0][j-1])
            else:
                dp1[0][j]=dp1[0][j-1]+coins[0][j]
                dp2[0][j]=dp2[0][j-1]+coins[0][j]
        for i in range(1,r):
            for j in range(1,c):
                dp0[i][j]=max(dp0[i-1][j],dp0[i][j-1])+coins[i][j]
                if coins[i][j]<0:
                    dp1[i][j]=max(dp0[i-1][j],dp0[i][j-1],dp1[i-1][j]+coins[i][j],dp1[i][j-1]+coins[i][j])
                    dp2[i][j]=max(dp1[i-1][j],dp1[i][j-1],dp2[i-1][j]+coins[i][j],dp2[i][j-1]+coins[i][j])
                else:
                    dp1[i][j]=max(dp1[i-1][j],dp1[i][j-1])+coins[i][j]
                    dp2[i][j]=max(dp2[i-1][j],dp2[i][j-1])+coins[i][j]
        return dp2[-1][-1]
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

## 树状数组

![树状数组入门树状数组教程树状数组详细讲解](https://pic.leetcode.cn/1717549976-yUVqsj-lc307.png)

每个小区间的长度就是右端点的lowbit值。根据这个特征和图示就可以构建和使用树状数组。注意树状数组的索引从1开始。

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

## 堆

默认是小顶堆，可以做到将最小的元素放在堆顶，随时弹出堆顶，加入新的元素。可以通过存储相反数来实现大顶堆。

#### 列表模拟堆

```python
class Heap:
    def __init__(self):
        self.tree=[]
    def down_to_up(self,i):
        while (i-1)//2>=0:
            if self.tree[i]<self.tree[(i-1)//2]:
                self.tree[i],self.tree[(i-1)//2]=self.tree[(i-1)//2],self.tree[i]
                i=(i-1)//2
            else:
                break
    def up_to_down(self,i):
        while i*2+1<len(self.tree):
            if i*2+2>=len(self.tree):
                child=i*2+1
            else:
                if self.tree[i*2+1]<=self.tree[i*2+2]:
                    child=i*2+1
                else:
                    child=i*2+2
            if self.tree[i]>self.tree[child]:
                self.tree[i],self.tree[child]=self.tree[child],self.tree[i]
                i=child
            else:
                break
    def heappush(self,num):
        self.tree.append(num)
        self.down_to_up(len(self.tree)-1)
    def heappop(self):
        if not self.tree:
            return None
        self.tree[0],self.tree[-1]=self.tree[-1],self.tree[0]
        ans=self.tree.pop()
        self.up_to_down(0)
        return ans
heap=Heap()
n=int(input())
for _ in range(n):
    l=list(map(int,input().split()))
    if l[0]==1:
        heap.heappush(l[1])
    elif l[0]==2:
        print(heap.heappop())
```

#### 双堆

计算最小的前n个序列和

在读取数据的过程中，动态维护截止到当前行元素的最小n个序列和的小顶堆。在计算下一个堆时，需要暂时构建一个大顶堆，便于将新的更小元素入堆，将最大的元素弹出。

```python
T=int(input())
import heapq
for _ in range(T):
    m,n=map(int,input().split())
    arr=list(map(int,input().split()))
    heap=arr[:]
    heapq.heapify(heap)
    for _ in range(m-1):
        n_arr=list(map(int,input().split()))
        n_arr.sort()
        n_heap=[]
        while heap:
            re_num=heapq.heappop(heap)
            for num in n_arr:
                n_num=re_num+num
                if len(n_heap)<n:
                    heapq.heappush(n_heap,-n_num)
                else:
                    if n_num<-n_heap[0]:
                        heapq.heappop(n_heap)
                        heapq.heappush(n_heap,-n_num)
                    else:
                        break
        heap=[-x for x in n_heap]
        heapq.heapify(heap)
    heap.sort()
    print(*heap)
```



## Tire（前缀树，字典树）

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

## 平衡二叉搜索树AVL

节点的平衡值balance定义为左边最大深度减右边最大深度

调整AVL的基本操作是左旋和右旋，如下图所示：

左旋：

```txt
        z                         y
       / \                       / \
      T1  y       ----->        z   T3
         / \                   / \
        T2 T3                 T1 T2
```

右旋：

```txt
         y                    x
        / \                  / \
       x   T3    ----->     T1  y
      / \                      / \
     T1 T2                    T2 T3
```

当树失衡时，有以下四种情况

左边深度大（balance>1）

1.LL型

新节点插入到了node的左子树的左侧（value<node.left.value)

```txt
         x                    y
        /                    / \
       y         ----->     z   x
      / 
     z
```

此时直接右旋。

2.LR型

新节点插入到了node的左子树右侧（value>node.left.value)

```txt
         y                    z
        /                    / \
       x         ----->     x   y
        \                      
         z                    
```

此时先对左子节点左旋，再整体右旋。

右边深度大（balance<-1）

3.RR型

新节点插入到了node的右子树的右侧（value>node.right.value)

```txt
         x                     y
          \                   / \
           y      ----->     x   z
            \   
             z     
```

此时直接右旋。

4.RL型

新节点插入到了node的右子树的左侧（value<node.right.value)

```txt
        x                         z
         \                       / \
          y       ----->        x   y
         /                     
        z                      
```

此时先对右子节点右旋，再整体左旋。

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
```



## 数学相关

#### 欧式筛法

筛选素数

```python
def euler_sieve(n):
    is_prime=[True]*(n+1)
    is_prime[0]=is_prime[1]=False
    primes=[]
    for i in range(2,n+1):
        if is_prime[i]:
            primes.append(i)
        for p in ptimes:
            if i*p>n:
                break
            is_primes[i*p]=False
            if i%p==0:
                break
    return primes
```

#### 康托展开

> 计算排列方式的顺序数

对于P=[a1,a2,...,an]中全部元素的一种排列，其康托展开值x的计算公式为：

x=a1(n-1)!+a2(n-2)!+......+an-11!+an0!

其中ai是该位数字在剩余未使用数字中的排名（从0开始）

#### Catalan数

C0=1; C1=1; Cn=((4n-2)*Cn-1)//(n+1) 

应用场景：有多少个合法出栈序列，合法括号序列数量，满节点二叉树数量，凸多边形的三角划分种类数，

n个运算符的表达式加括号方式数，n*n网格只能向上或向右的不穿过对角线的路径数量

#### 最大公约数GCD和最小公倍数LCM

```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a

def lcm(a, b):
    return abs(a * b) // gcd(a, b)
```



