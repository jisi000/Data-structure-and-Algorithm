# DSA Assignment #A: 5月份月考

*Updated 2026-05-06 15:43 GMT+8*
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

### E04137: 最小新整数 

monotonic stack, http://cs101.openjudge.cn/practice/04137/

思路：单调递增栈，限制出栈次数。



代码：

```python
t=int(input())
def new_int(n,k):
    l=list(str(n))
    length=len(l)-k
    stack=[]
    for i in range(len(l)):
        re=l[i]
        while stack and int(stack[-1])>int(re) and k>0:
            stack.pop()
            k-=1
        stack.append(re)
    return ''.join(stack[:length])
for _ in range(t):
    n,k=map(int,input().split())
    print(new_int(n,k))
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260507105228867](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260507105228867.png)



### E04143: 和为给定数 

two pointers, http://cs101.openjudge.cn/dsapre/04143/


思路：哈希表



代码：

```python
n=int(input())
arr=sorted(list(map(int,input().split())))
m=int(input())
ans=[]
l=set()
for num in arr:
    if m-num in l:
        ans.append((m-num,num))
    l.add(num)
ans.sort(key=lambda x:x[0])
if ans:
    print(*ans[0])
else:
    print('No')
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260507105317965](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260507105317965.png)



### M27638: 求二叉树的高度和叶子数目

http://cs101.openjudge.cn/practice/27638/

思路：提前创建节点。递归式遍历。



代码：

```python
class Node:
    def __init__(self,val):
        self.val=val
        self.left=None
        self.right=None
n=int(input())
children=set()
nodes=[Node(i) for i in range(n)]
for i in range(n):
    l,r=map(int,input().split())
    if l!=-1:
        children.add(l)
        nodes[i].left=nodes[l]
    if r!=-1:
        children.add(r)
        nodes[i].right=nodes[r]
root=None
for i in range(n):
    if i not in children:
        root=nodes[i]
        break
ans=[0,0]
def traversal(node,high):
    if not node.left and not node.right:
        ans[1]+=1
        ans[0]=max(ans[0],high)
    if node.left:
        traversal(node.left,high+1)
    if node.right:
        traversal(node.right,high+1)
traversal(root,0)
print(*ans)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260507105455466](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260507105455466.png)



### M30720: 败方树的构建与维护

http://cs101.openjudge.cn/practice/30720/

思路：为了实现相应功能，树的节点需要储存更多信息，包括胜者和败者的值以及指向父节点的指针。

初始构建使用双端队列实现，构建过程类似哈夫曼编码树。层序遍历使用BFS。

维护时，利用父节点指针，自底向上层层修改。由于每个节点向其父节点传递的信息是胜者的值，因此当胜者值没有改变时，signal=False提前终止。同时需要记录修改前传递的值是什么，才能知道要修改父节点的哪个值。

代码：

```python
class Node:
    def __init__(self,win=None,lose=None):
        self.win=win
        self.lose=lose
        self.left=None
        self.right=None
        self.father=None
import sys
from collections import deque
def main():
    data=list(map(int,sys.stdin.read().split()))
    n,m=data[0],data[1]
    idx=2
    arr=[]
    for _ in range(n):
        num=data[idx]
        idx+=1
        arr.append(Node(num))
    queue=deque(arr)
    while len(queue)>1:
        l=queue.popleft()
        r=queue.popleft()
        fa=Node()
        fa.win=min(l.win,r.win)
        fa.lose=max(l.win,r.win)
        fa.left=l
        fa.right=r
        l.father=fa
        r.father=fa
        queue.append(fa)
    root=queue[0]
    def traversal(node):
        q=deque([node])
        l=[node.win]
        while q:
            re=q.popleft()
            if not re.left and not re.right:
                continue
            l.append(re.lose)
            q.append(re.left)
            q.append(re.right)
        return l
    print(*traversal(root))
    for _ in range(m):
        index,val=data[idx],data[idx+1]
        idx+=2
        signal=True
        node=arr[index]
        last=node.win
        node.win=val
        while signal and node.father:
            fa=node.father
            if fa.win==last:
                fa.win=node.win
                if fa.win!=min(fa.win,fa.lose):
                    fa.win,fa.lose=fa.lose,fa.win
            else:
                fa.lose=node.win
                last=fa.win
                if fa.win==min(fa.win,fa.lose):
                    signal=False
                else:
                    fa.win,fa.lose=fa.lose,fa.win
            node=fa
        print(*traversal(root))
if __name__=='__main__':
    main()
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260507164326701](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260507164326701.png)



### 27093: 排队又来了

Segment Tree, Discretization（离散化）, binary search, http://cs101.openjudge.cn/practice/27093/

思路：目前没有遇到树状数组无法解决，必须使用线段树的题，因此还是使用了树状数组（并懒得学看上去更繁琐的线段树）。

可以互换的一组数字存在于同一层，因此这些数字的顺序可以任意排列。当一个数字前面有无法互换的数字时，就把他放在下一层。让每个数字都达到能到的最浅层，就能使尽可能小的数字放在最前面，因此每个数字的层都是层数最大的阻碍层+1

实现时，先把数字离散化，按顺序赋予新索引值，用左树状数组tree_left记录比当前数字小的全部数字所在的最大层。用右树状数组tree_right记录比当前数字大的全部数字所在的最大层。因为树状数组能储存从1到当前索引的信息，因此右树状数组的索引需要倒置。

代码

```python
import bisect
N,D=map(int,input().split())
arr=list(map(int,input().split()))
s=sorted(list(set(arr)))
n=len(s)
num_to_index={num:i+1 for i,num in enumerate(s)}
def low_bit(x):
    return x&-x
def update(index,delta,tree):
    while index<n:
        tree[index]=max(tree[index],delta)
        index+=low_bit(index)
def query(index,tree):
    result=-1
    while index>0:
        result=max(tree[index],result)
        index-=low_bit(index)
    return result
tree_left=[-1]*(n+1)
tree_right=[-1]*(n+1)
l=[]
for num in arr:
    idx=num_to_index[num]
    left=bisect.bisect_right(s,num-D-1)
    right=bisect.bisect_left(s,num+D+1)
    order=max(query(left,tree_left),query(n-right,tree_right))+1
    if len(l)<order+1:
        l.append([])
    l[order].append(num)
    update(idx,order,tree_left)
    update(n-idx+1,order,tree_right)
ans=[]
for li in l:
    ans.extend(sorted(li))
print(*ans)
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260507105104620](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260507105104620.png)



### T30669: 地铁换乘

LCA, binary lifting, http://cs101.openjudge.cn/practice/30669/

思路：倍增法寻找LCA的模板题。找到LCA后需要计算用时和相遇点深度。



代码

```python
import sys
def main():
    data = list(map(int, sys.stdin.read().split()))
    n,t=data[0],data[1]
    LOG=19
    graph=[[] for _ in range(n+1)]
    idx=2
    for _ in range(n-1):
        u,v=data[idx],data[idx+1]
        idx+=2
        graph[u].append(v)
        graph[v].append(u)
    p,q,v1,v2=data[idx],data[idx+1],data[idx+2],data[idx+3]
    up=[[0]*(n+1) for _ in range(LOG)]
    depth=[0]*(n+1)
    stack=[(t,0)]
    while stack:
        u,fa=stack.pop()
        up[0][u]=fa
        for v in graph[u]:
            if v!=fa:
                depth[v]=depth[u]+1
                stack.append((v,u))
    for k in range(1,LOG):
        for u in range(1,n+1):
            up[k][u]=up[k-1][up[k-1][u]]
    def lca(u,v):
        if depth[u]<depth[v]:
            u,v=v,u
        for k in range(LOG-1,-1,-1):
            if depth[u]-(1<<k)>=depth[v]:
                u=up[k][u]
        if u==v:
            return u
        for k in range(LOG-1,-1,-1):
            if up[k][u]!=up[k][v]:
                u=up[k][u]
                v=up[k][v]
        return up[0][u]
    r=lca(p,q)
    a,b,c=depth[p],depth[q],depth[r]
    l=a+b-2*c
    d=l//(v1+v2)
    dep=abs(v1*d-a+c)+c
    print(d,dep)
if __name__ == '__main__':
    main()
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260507135427579](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260507135427579.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

AC5，很舒适的一次月考。前三题都一次过，第四题看到通过率一直是0就果断跳过，第五题是之前做过的题，第六题抄了cheat sheet上的模板。还有点时间回看第四题，发现不知道如何解决n非2的幂次的情况，就直接放弃了这道题。

考试结束后把n非2的幂次的数据去掉了，我才着手写了一下这道题，发现又是一道树相关知识的大杂烩，建树方式借鉴了哈夫曼编码树，设置从子节点指向父节点的指针也有一点双向链表的启发在。



