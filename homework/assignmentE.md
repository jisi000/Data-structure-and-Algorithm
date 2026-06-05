# DSA Assignment E: 20260603期末机考

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

### E30646:缺失的第一个正数

http://cs101.openjudge.cn/practice/30646

思路：确定遍历左右边界。

右边界：n个数，使缺失的第一个正数最大的方法就是给1到n，这时缺失的数是n+1。因此遍历右边界是n+1

左边界：要求正数，因此要从1遍历。从0遍历是可能的WA原因，这个样例中看不出来。

代码：

```python
n=int(input())
arr=set(map(int,input().split()))
for i in range(1,n+2):
    if i not in arr:
        print(i)
        break
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260605155345246](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260605155345246.png)



### E30930:猫猫水群聊

 http://cs101.openjudge.cn/practice/30930


思路：二分查找



代码：

```python
def check(x):
    c=0
    for num in arr:
        if num>=x:
            c+=1
    return c>=x
n=int(input())
arr=list(map(int,input().split()))
lo=0
hi=n+1
while lo<hi:
    mid=(lo+hi)//2
    if check(mid):
        lo=mid+1
    else:
        hi=mid
print(hi-1)

```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260605153319926](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260605153319926.png)



### M30874:匹配队友

http://cs101.openjudge.cn/practice/30874

思路：有三种不同的职业，如果放到同一个队列里就很麻烦。因此给三种职业开三个队列与三个计数器。



代码：

```python
from collections import deque
n=int(input())
career=list(input().split())
queT=deque()
queH=deque()
queD=deque()
cT,cH,cD=0,0,0
idx=[0]*n
q=1
for i in range(n):
    if career[i]=='T':
        queT.append(i)
        cT+=1
    elif career[i]=='H':
        queH.append(i)
        cH+=1
    elif career[i]=='D':
        queD.append(i)
        cD+=1
    if cT>=1 and cH>=1 and cD>=3:
        t=queT.popleft()
        idx[t]=q
        h=queH.popleft()
        idx[h]=q
        d1=queD.popleft()
        idx[d1]=q
        d2=queD.popleft()
        idx[d2]=q
        d3=queD.popleft()
        idx[d3]=q
        cT-=1
        cH-=1
        cD-=3
        q+=1
print(*idx)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260605153901061](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260605153901061.png)



### M30680:森林局部排序遍历

http://cs101.openjudge.cn/practice/30680

思路：构建邻接表，统计入度。入度为零的是根节点。然后按照题目要求递归式遍历。



代码：

```python
n=int(input())
graph={}
in_degree={}
for i in range(n):
    nums=list(map(int,input().split()))
    root=nums[0]
    graph[root]=nums[1:]
    if root not in in_degree:
        in_degree[root]=0
    for num in nums[1:]:
        if num not in in_degree:
            in_degree[num]=0
        in_degree[num]+=1
ans=[]
def traversal(node):
    l=graph[node][:]
    l.append(node)
    l.sort()
    for num in l:
        if num==node:
            ans.append(num)
        else:
            traversal(num)
roots=[]
for node in in_degree:
    if in_degree[node]==0:
        roots.append(node)
roots.sort()
for node in roots:
    traversal(node)
for num in ans:
    print(num)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260605153445389](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260605153445389.png)



### M30947:Ask for Likes

http://cs101.openjudge.cn/practice/30947

思路：

困扰点：看到这道题第一反应是做标准质因数分解（利用cheat sheet中欧式筛法的模板）。然后在dfs上遇到了困难，因为任意几个质因数的乘积都是一个新的因数，搜索时如何把这些可能的组合搜全呢？似乎很难实现。因此换个思路，这道题真正需要的是得到全部因数，而不是质因数。

此外，由于目标是使表情包数量的乘积等于目标值，我也陷入了一个给目标数做拆分，然后考虑拆分出数字的数量和表情包数量是否相等的误区。其实这也是不必要的，因为表情包数量列表中可能有多个‘1’和‘0’。真正的限制因素是能否把大于1的数字都使用到。

以上的困难用dfs把大问题拆成子问题的思路就都可以解决。可以对表情包数量列表进行从大到小遍历，每次尝试一个因数，然后子问题就成为了：对于除以这个因数后的待配凑数字和表情包列表剩余的数字，是否能构成相等的乘积？表情包列表遍历到末尾，或者遍历到1，就说明成功了。

一个可能导致WA的细节：待配凑数字本身也应该被放入因数表，例如表情包数量列表中只有一个数字，其小于等于待配凑数字，大于除了它本身的最大因数，那么如果不把待配凑数字放入因数表就不能返回Yes

剪枝：如果倒序排列后的表情包数量数组的第30个数字也大于等于2，这时总乘积必然超过10**9，可以全部返回No。如果不是这样，可以先计算数组中所有非0数字的乘积，小于这个乘积的查询可以直接返回No。

代码

```python
import bisect,sys
from functools import lru_cache
from collections import deque

#防止数据读取耗时太长
def it():
    for line in sys.stdin.buffer:
        for di in line.split():
            yield int(di)

#判断数字能否达成目标
def judge(x):
    factors = deque()
    for i in range(int(x ** 0.5), 0, -1):
        if x%i==0:
            if i*i==x:
                factors.append(i)
            else:
                factors.appendleft(i)
                factors.append(x//i)
    factors=list(factors)
    @lru_cache(maxsize=None)
    def dfs(num,idx):
        re=arr[idx]
        if re>num:
            return False
        if idx==n-1 or re<=1:
            return True
        loc=bisect.bisect_left(factors,re)
        for factor in factors[loc:]:
            if factor>num:
                return False
            if num%factor==0:
                if dfs(num//factor,idx+1):
                    return True
        return False
    return dfs(x,0)

data=it()
n=next(data)
q=next(data)
arr=[next(data) for _ in range(n)]
arr.sort(reverse=True)
if len(arr)>=30 and arr[29]>=2:
    for _ in range(q):
        __=next(data)
        print('No')
else:
    s = 1
    for m in arr:
        if m <= 1:
            break
        s *= m
    for _ in range(q):
        x=next(data)
        if x<s:
            print('No')
        elif x>s:
            print('Yes' if judge(x) else 'No')
        else:
            print('Yes')
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260605152327344](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260605152327344.png)



### T30913:猫猫逛公园 

http://cs101.openjudge.cn/practice/30913

思路：这道题体现了tarjan算法的优越性，可以直接得到按逆拓扑序排列的scc列表，省去了拓扑排序。

愉悦度计算：scc内部的路径可以榨干所有路径，但是scc间的路径只能走过一次。

先接收邻接表，得到scc列表。然后缩点，构建DAG。构建DAG的过程就是遍历边的过程，因此同时也可以计算一下scc内部路径的总愉悦度，并且DAG中也记录一下scc间权值最大的路径。

难点：这道题是一个固定起点的路径查找，而不是从入度为零的节点开始查找。因此第一反应可能是使用dfs来搜索，但是这样时间和空间的开销都太大，一个节点可能被多次查找，失去了拓扑排序的意义。如何判断节点可及性也是需要考虑的问题。

这道题中的入度其实意义不大，因为即使一个节点的入度不为零，只要能从起点到这里，这个节点就是可用的。因此判断节点是否可用的方法是把dp列表的初始值设置为-1，只有起点是scc内部的正权值。这样只要按照拓扑序（也就是scc列表的逆序）遍历，把dp值不为-1的点向下延伸。

WA：这道题中节点自环和重边都算作有效的不同边，如果代码忽略这些就会WA。

MLE：这道题数据量很大，因此如果在建邻接表和记录边权的时候不节省就会MLE，如果最后一步用递归式dfs来查找也会导致超空间。我一开始建了一个只保存节点的邻接表和一个保存边权的字典，会超空间。把边权合并到邻接表中就能通过了。tarjan算法中的dfn列表可以判断节点是否被使用过，所以不用担心重边。

代码

```python
import sys
import math
sys.setrecursionlimit(10**6)
def tarjan_scc(graph):
    n=len(graph)
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
        for v,w in graph[u]:
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
def data():
    for line in sys.stdin.buffer:
        for token in line.split():
            yield int(token)
def cal(w):
    t=math.ceil(((8*w+1)**0.5-1)/2)
    wt=t*w-((t+1)*t*(t-1))//6
    return wt
def main():
    it=data()
    n=next(it)
    m=next(it)
    graph=[[] for _ in range(n)]
    for _ in range(m):
        u=next(it)-1
        v=next(it)-1
        w=next(it)
        graph[u].append((v,w))
    sccs=tarjan_scc(graph)
    l=len(sccs)
    scc_id=[0]*n
    scc_w=[0]*l
    for i,comp in enumerate(sccs):
        for node in comp:
            scc_id[node]=i
    DAG=[dict() for _ in range(l)]
    for u in range(n):
        for v,w in graph[u]:
            su,sv=scc_id[u],scc_id[v]
            if su!=sv:
                if sv not in DAG[su]:
                    DAG[su][sv]=0
                DAG[su][sv]=max(DAG[su][sv],w)
            else:
                scc_w[su]+=cal(w)
    dp=[-1]*l
    start=next(it)-1
    s=scc_id[start]
    dp[s]=scc_w[s]
    for u in range(l-1,-1,-1):
        if dp[u]!=-1:
            for v,w in DAG[u].items():
                dp[v]=max(dp[v],dp[u]+w+scc_w[v])
    print(max(dp))
if __name__=='__main__':
    main()
```



<mark>（至少包含有"Accepted"）</mark> 

![image-20260605153552550](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260605153552550.png)

### U30919:猫猫去旅行 

http://cs101.openjudge.cn/practice/30919

思路：看了题解中的提示，了解了总往返距离最短的居住点是中位数。于是需要动态维护中位数，计算数字与中位数总偏差。计算中位数的方法是双堆，一个储存较大的一半数字的小顶堆和较小的一半数字的大顶堆。



代码

```python
import heapq
n=int(input())
points=list(map(int,input().split()))
def mid(arr):
    l=[0]*(n+1)
    left=[]
    right=[]
    re=arr[0]
    heapq.heappush(left,-re)
    sum_left=re
    sum_right=0
    for i in range(1,n):
        re=arr[i]
        if re<=-left[0]:
            heapq.heappush(left,-re)
            sum_left+=re
        else:
            heapq.heappush(right,re)
            sum_right+=re
        if len(left)>len(right)+1:
            m=-heapq.heappop(left)
            sum_left-=m
            heapq.heappush(right,m)
            sum_right+=m
        elif len(right)>len(left):
            m=heapq.heappop(right)
            sum_right-=m
            heapq.heappush(left,-m)
            sum_left+=m
        l[i+1]=sum_right-sum_left-left[0]*(len(left)-len(right))
    return l
pre_l=mid(points)
pre_r=mid(points[::-1])
ans=float('inf')
for t in range(n+1):
    s=pre_l[t]+pre_r[n-t]
    ans=min(ans,s)
print(2*ans)
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260605173144335](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260605173144335.png)



## 2. 课程总结

第六题，很遗憾MLE，但是在有限时间内对体量如此大的代码进行优化的确并不容易。因为想优化掉一个字典，整个代码的很多逻辑都需要重写，还涉及会不会出现新的bug的问题，没有在时间限制内改出来也不算意外。

第五题，考到了一个更像是计概题的math+dfs。这学期我一直在针对树和图进行dfs搜索，这种把大问题化成子问题的偏计概的dfs很久没有练过了，所以想了很久也没有有效的思路，参考其他同学的解答后算是把这个回忆起来了。感觉第五题是我在这次考试中比较意外的地方，很不巧的考了我不擅长的math+dfs。

在这次作业中着重写了第五、六题的思路，包含一些我在做题的过程中遇到的困难和解决方案，或许有的共性问题可以帮助到其他没做出来的同学。

我从上学期的计概就开始跟着闫老师学习，数算课的日常学习对我来说是比上学期的计概轻松的，因为在寒假做了一些预习，而且数算的课程内容比较有规律性，模板和固定算法很多，做作业主要是学习课上讲的模板然后进行应用。我这学期的学习重点也放在了理解和应用算法模板上，而不是对着一些思路神奇的题目死磕。最终机考的AC4好像也不足以证明这样的学习方法特别有效，但我觉得这个学习思路对于没有编程基础，完全是上大学之后才开始学习编程的人或许有帮助。

这次我从学期初就一直在动态完善cheat sheet，可以算作是我的数算学习完整笔记了。在这次考试中也用到了这份笔记中的知识点，比如二分查找模板，强连通分量的tarjan算法以及DAG的构建。这里再放一下链接 https://github.com/jisi000/Data-structure-and-Algorithm/blob/main/cheat%20sheet.md
