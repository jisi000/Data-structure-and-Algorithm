# DSA Assignment #B: 20260513模拟考

*Updated 2026-05-13 13:35 GMT+8*
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

### E02724: 生日相同

sortings, http://cs101.openjudge.cn/pctbook/E02724/

思路：注意没有两个及以上生日相同的人就不输出



代码：

```python
from collections import defaultdict
n=int(input())
d=defaultdict(list)
for _ in range(n):
    idx,a,b=input().split()
    a,b=int(a),int(b)
    d[(a,b)].append(idx)
ks=sorted(d.keys())
for k in ks:
    if len(d[k])>1:
        print(*k,end=' ')
        print(*d[k])
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260513172928811](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260513172928811.png)



### E19963: 买学区房

math, http://cs101.openjudge.cn/practice/19963


思路：按题目要求计算和查找,注意中位数分奇偶



代码：

```python
n=int(input())
pairs=[i[1:-1] for i in input().split()]
distance=[sum(map(int,pair.split(','))) for pair in pairs]
prices=list(map(int,input().split()))
rates_raw=[i/j for i,j in zip(distance,prices)]
rates=sorted(rates_raw)
if len(rates)%2==0:
    rates_middle=(rates[(len(rates)//2)-1]+rates[len(rates)//2])/2
else:
    rates_middle=rates[len(rates)//2]
prices1 = sorted(prices)
if len(prices1) % 2 == 0:
    prices_middle = (prices1[(len(prices1) // 2) - 1] + prices1[len(prices1) // 2]) / 2
else:
    prices_middle = prices1[len(prices1) // 2]
count=0
for i in range(n):
    if rates_raw[i]>rates_middle and prices[i]<prices_middle:
        count+=1
print(count)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260513173050474](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260513173050474.png)



### M20746: 满足合法工时的最少人数

binary search, http://cs101.openjudge.cn/practice/20746/

思路：二分查找。因为员工数为正整数，所以lo为1。无论如何每项工作都至少需要1个工作时，因此hi为最大的任务工时。



代码：

```python
from math import ceil
def check(x,mx):
    s=0
    for t in l:
        s+=ceil(t/x)
    return s<=mx
l=list(map(int,input().split(',')))
mx=int(input())
lo,hi=1,max(l)+1
while lo<hi:
    mid=(lo+hi)//2
    if check(mid,mx):
        hi=mid
    else:
        lo=mid+1
print(hi)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260513173524783](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260513173524783.png)



### M07734: 虫子的生活

DSU, http://cs101.openjudge.cn/practice/07734/

思路：因为虫子有两种性别，因此开二倍大小的数组。i表示自身性别，i+n表示相反性别

如果两种虫子能交配，非同种性别，就合并a和b+n，a+n和b。

通过对交配的虫子检查 if uf.find(a)==uf.find(b) or uf.find(a+n)==uf.find(b+n) 检出同性恋



代码：

```python
class UnionFind:
    def __init__(self,n):
        self.parent=list(range(2*n+1))
        self.rank=[0]*(2*n+1)
    def find(self,x):
        while self.parent[x]!=x:
            self.parent[x]=self.parent[self.parent[x]]
            x=self.parent[x]
        return x
    def union(self,x,y):
        rx,ry=self.find(x),self.find(y)
        if rx!=ry:
            if self.rank[rx]>self.rank[ry]:
                self.parent[ry]=rx
            elif self.rank[rx]<self.parent[ry]:
                self.parent[rx]=ry
            else:
                self.parent[ry]=rx
                self.rank[rx]+=1
def judge(n,m):
    uf=UnionFind(n)
    ok=True
    for _ in range(m):
        a,b=map(int,input().split())
        if uf.find(a)==uf.find(b) or uf.find(a+n)==uf.find(b+n):
            ok=False
        uf.union(a,b+n)
        uf.union(a+n,b)
    return ok
def main():
    N=int(input())
    for i in range(N):
        n,m=map(int,input().split())
        if i!=0:
            print('')
        print(f'Scenario #{i+1}:')
        if judge(n,m):
            print('No suspicious bugs found!')
        else:
            print('Suspicious bugs found!')
if __name__=='__main__':
    main()
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260513173844567](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260513173844567.png)



### M02186: Popular Cows

SCC, http://cs101.openjudge.cn/practice/02186/

思路：SCC内元素互相认为popular；上游的SCC中全部元素都支持下游的SCC中全部元素；每个SCC内的元素要么全都是答案，要么全不是答案。

根据以上思考，把图转变为SCC构成的DAG，统计每个SCC的元素数量，每次传递popular都使用整个SCC的元素数量。按SCC的拓扑序（即tarjan算法得到的sccs列表的逆序）dp。



代码

```python
import sys
sys.setrecursionlimit(10**6)
def tarjan(graph,n):
    dfn=[-1]*n
    low=[-1]*n
    stack=[]
    on_stack=[False]*n
    sccs=[]
    timer=0
    def dfs(u):
        nonlocal timer
        dfn[u]=low[u]=timer
        timer+=1
        stack.append(u)
        on_stack[u]=True
        for v in graph[u]:
            if dfn[v]==-1:
                dfs(v)
                low[u]=min(low[u],low[v])
            elif on_stack[v]:
                low[u]=min(low[u],dfn[v])
        if low[u]==dfn[u]:
            component=[]
            while True:
                node=stack.pop()
                on_stack[node]=False
                component.append(node)
                if node==u:
                    break
            sccs.append(component)
    for i in range(n):
        if dfn[i]==-1:
            dfs(i)
    return sccs
def main():
    data=list(map(int,sys.stdin.read().split()))
    N,M=data[0],data[1]
    graph=[[] for _ in range(N)]
    idx=2
    for _ in range(M):
        u,v=data[idx],data[idx+1]
        u-=1
        v-=1
        graph[u].append(v)
        idx+=2
    sccs=tarjan(graph,N)
    scc_count=[0]*len(sccs)
    po_count=[0]*len(sccs)
    dng=[set() for _ in range(len(sccs))]
    re_dng=[set() for _ in range(len(sccs))]
    scc_idx=[-1]*N
    for i,scc in enumerate(sccs):
        s=0
        for node in scc:
            scc_idx[node]=i
            s+=1
        po_count[i]=s
        scc_count[i]=s
    for u in range(N):
        for v in graph[u]:
            if scc_idx[u]!=scc_idx[v]:
                dng[scc_idx[u]].add(scc_idx[v])
                re_dng[scc_idx[v]].add(scc_idx[u])
    for i in range(len(sccs)-1,-1,-1):
        s=po_count[i]
        for j in dng[i]:
            po_count[j]+=s
    ans=0
    for i in range(len(sccs)):
        if po_count[i]==N:
            ans+=scc_count[i]
    print(ans)
if __name__=='__main__':
    main()
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260513175549807](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260513175549807.png)



### T01236: Network of Schools 238

SCC, http://cs101.openjudge.cn/practice/01236/

思路：计算最少需要添加多少条边，使得整个图变成强连通，等价于在缩点后的DAG中，计算入度为0的节点数（记为in0）和出度为0的节点数（记为out0），然后取两者的最大值。

所以先按模板缩点，然后计算DAG中元素的入度和出度（出度即为DAG[i]的长度）。SubtaskA的答案是in0，SubtaskB的答案是max(in0,out0)

注意整个图能成为一个SCC的情况要单独讨论，因为此时SubtaskA的答案是1，而不是0。

代码

```python
import sys
sys.setrecursionlimit(10**6)
def tarjan(graph,n):
    dfn=[-1]*n
    low=[-1]*n
    stack=[]
    on_stack=[False]*n
    sccs=[]
    timer=0
    def dfs(u):
        nonlocal timer
        dfn[u]=low[u]=timer
        timer+=1
        stack.append(u)
        on_stack[u]=True
        for v in graph[u]:
            if dfn[v]==-1:
                dfs(v)
                low[u]=min(low[u],low[v])
            elif on_stack[v]:
                low[u]=min(low[u],dfn[v])
        if low[u]==dfn[u]:
            component=[]
            while True:
                node=stack.pop()
                on_stack[node]=False
                component.append(node)
                if node==u:
                    break
            sccs.append(component)
    for i in range(n):
        if dfn[i]==-1:
            dfs(i)
    return sccs
def main():
    data=list(map(int,sys.stdin.read().split()))
    N=data[0]
    graph=[[] for _ in range(N)]
    idx=1
    for i in range(N):
        while True:
            node=data[idx]
            idx+=1
            if node==0:
                break
            node-=1
            graph[i].append(node)
    sccs=tarjan(graph,N)
    ms=len(sccs)
    if ms==1:
        print(1)
        print(0)
        return
    scc_count=[0]*ms
    po_count=[0]*ms
    dng=[set() for _ in range(ms)]
    scc_idx=[-1]*N
    for i,scc in enumerate(sccs):
        s=0
        for node in scc:
            scc_idx[node]=i
            s+=1
        po_count[i]=s
        scc_count[i]=s
    for u in range(N):
        for v in graph[u]:
            if scc_idx[u]!=scc_idx[v]:
                dng[scc_idx[u]].add(scc_idx[v])
    in0,out0=0,0
    in_degree=[0]*ms
    for dn in dng:
        if len(dn)==0:
            out0+=1
        else:
            for i in dn:
                in_degree[i]+=1
    for i in range(ms):
        if in_degree[i]==0:
            in0+=1
    print(in0)
    print(max(in0,out0))
if __name__=='__main__':
    main()
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260513180532773](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260513180532773.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

成功AK，有三道题以前都做过，其余三道新题考到了二分查找和SCC两个知识点。二分查找很熟悉了，SCC昨天刚学过。而且有两道题都是SCC，直接复制粘贴，也节约了时间，这样都差点敲不完代码，数算很考验手速了。

这次模考难点在于识别tag，Popular Cows一开始没发现是SCC，但样例很友好的给了一个环，让我发现需要先缩点。

Network of Schools一开始对SubtaskB没有思路，画了很多图才想出来：计算最少需要添加多少条边，等价于在缩点后的DAG中，计算入度为0的节点数和出度为0的节点数，然后取两者的最大值。

