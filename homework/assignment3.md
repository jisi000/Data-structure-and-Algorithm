# Assignment #3: 20260311 cs201 Mock Exam

*Updated 2026-03-11 15:24 GMT+8*
 *Compiled by <mark>孙婧斯、生命科学学院</mark> (2026 Spring)*



**作业的各项评分细则及对应的得分**

| 标准                                 | 等级                                                         | 得分 |
| ------------------------------------ | ------------------------------------------------------------ | ---- |
| 按时提交                             | 完全按时提交：1分<br/>提交有请假说明：0.5分<br/>未提交：0分  | 1 分 |
| 源码、耗时（可选）、解题思路（可选） | 提交了4个或更多题目且包含所有必要信息：1分<br/>提交了2个或以上题目但不足4个：0.5分<br/>少于2个：0分 | 1 分 |
| AC代码截图                           | 提交了4个或更多题目且包含所有必要信息：1分<br/>提交了2个或以上题目但不足4个：0.5分<br/>少于：0分 | 1 分 |
| 清晰头像、PDF文件、MD/DOC附件        | 包含清晰的Canvas头像、PDF文件以及MD或DOC格式的附件：1分<br/>缺少上述三项中的任意一项：0.5分<br/>缺失两项或以上：0分 | 1 分 |
| 学习总结和个人收获                   | 提交了学习总结和个人收获：1分<br/>未提交学习总结或内容不详：0分 | 1 分 |
| 总得分： 5                           | 总分满分：5分                                                |      |
>
>
>
>**说明：**
>
>1. **解题与记录：**
>
>      对于每一个题目，请提供其解题思路（可选），并附上使用Python或C++编写的源代码（确保已在OpenJudge， Codeforces，LeetCode等平台上获得Accepted）。请将这些信息连同显示“Accepted”的截图一起填写到下方的作业模板中。（推荐使用Typora https://typoraio.cn 进行编辑，当然你也可以选择Word。）无论题目是否已通过，请标明每个题目大致花费的时间。
>
>2. **课程平台：**课程网站位于Canvas平台（https://pku.instructure.com ）。该平台将在<mark>第2周</mark>选课结束后正式启用。在平台启用前，请先完成作业并将作业妥善保存。待Canvas平台激活后，再上传你的作业。
>
>3. **提交安排：**提交时，请首先上传PDF格式的文件，并将.md或.doc格式的文件作为附件上传至右侧的“作业评论”区。确保你的Canvas账户有一个清晰可见的本人头像，提交的文件为PDF格式，并且“作业评论”区包含上传的.md或.doc附件。
>3. **延迟提交：**如果你预计无法在截止日期前提交作业，请提前告知具体原因。这有助于我们了解情况并可能为你提供适当的延期或其他帮助。  
>
>请按照上述指导认真准备和提交作业，以保证顺利完成课程要求。



## 1. 题目

### E20742:泰波拿契數

implementation, http://cs101.openjudge.cn/practice/20742/

思路：设置好初始的三个数，根据n的大小递归。因为n最小为1，而T1=T2，因此直接输出c当n=1时也正确。

代码：

```python
a,b,c=0,1,1
n=int(input())
for _ in range(n-2):
    a,b,c=b,c,a+b+c
print(c)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260311162654723](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260311162654723.png)



### E30571.十进制整数的反码

bit manipulation, http://cs101.openjudge.cn/practice/E30571/

思路：每次取n的末位，将n右移位，然后把这个数字的反码加到答案上。



代码：

```python
n=int(input())
ans=0
i=0
while n>0:
    re=1-(n&1)
    n>>=1
    ans+=re*(2**i)
    i+=1
print(ans)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260311163157992](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260311163157992.png)



### E29950:稳定的符文序列

two pointers, http://cs101.openjudge.cn/practice/E29950



思路：双指针。先向右挪动右指针直到不能再挪动，记录当前长度，然后向右挪动左指针，使右指针可以继续挪动。



代码：

```python
s=input()
l,r=0,0
ans=1
re={}
while r<len(s):
    while r<len(s) and s[r] not in re:
        re[s[r]]=1
        r+=1
    ans=max(ans,r-l)
    while r<len(s) and s[r] in re:
        del re[s[l]]
        l+=1
print(ans)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260311162957436](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260311162957436.png)



### M30218:狭路相逢

stack, http://cs101.openjudge.cn/practice/M30218/



思路：因为勇士和怪兽分别用正负数表示，因此两者相加就是活着的一方剩余的血量。假定勇士不移动，怪兽向左移动。如果是怪兽，将其一直向前移动直到死亡或者前面没有勇士。如果怪兽和勇士有一方剩余血量不为零，将其入栈。



代码：

```python
N=int(input())
HP=list(map(int,input().split()))
stack=[]
for hp in HP:
    if hp>0:
        stack.append(hp)
    else:
        while hp<0 and stack and stack[-1]>0:
            hp+=stack.pop()
        if hp!=0:
            stack.append(hp)
print(len(stack))
print(*stack)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260311163319817](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260311163319817.png)



### M02299: Ultra-QuickSort

merge sort, http://cs101.openjudge.cn/practice/02299/

思路：要求计算逆序对数量，直接套用归并排序模板即可。唯一需要做的就是在归并时，如果当前右数组的数小于当前左数组的数，说明b[j]与a中的从第i个数到最后一个数都是逆序对，逆序对数加（len(a)-i）。



代码：

```python
def merge(a,b):
    i,j=0,0
    ans=[]
    while i<len(a) and j<len(b):
        if a[i]>b[j]:
            count[0]+=len(a)-i
            ans.append(b[j])
            j+=1
        else:
            ans.append(a[i])
            i+=1
    ans.extend(a[i:] or b[j:])
    return ans
def merge_sort(a):
    if len(a)<=1:
        return a
    mid=len(a)//2
    left=merge_sort(a[:mid])
    right=merge_sort(a[mid:])
    return merge(left,right)
while True:
    n=int(input())
    if n==0:
        break
    arr=[int(input()) for _ in range(n)]
    count=[0]
    merge_sort(arr)
    print(count[0])
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260311163659122](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260311163659122.png)



### M29954:逃离紫罗兰监狱

bfs, http://cs101.openjudge.cn/practice/29954 

思路：与通用bfs的区别是：

1.当k>0时可以进入墙体，因此queue中也要记录剩余的k，穿一次墙k-1

2.visited要记录的的是到达每个位置，剩余穿墙次数为rk时的状态是否访问过。不能只记录位置，否则可能因为在前面用了过多的穿墙次数，而用了较少穿墙次数的情况没有被记录，导致找不到答案。visited=[[[False]*(K+1) for _ in range(C)] for _ in range(R)]

代码：

```python
from collections import deque
R,C,K=map(int,input().split())
matrix=[list(input()) for _ in range(R)]
start,target=(-1,-1),(-1,-1)
for i in range(R):
    for j in range(C):
        if matrix[i][j]=='S':
            start=(i,j)
        elif matrix[i][j]=='E':
            target=(i,j)
queue=deque([(start,0,K)])
visited=[[[False]*(K+1) for _ in range(C)] for _ in range(R)]
visited[start[0]][start[1]][K]=True
flag=True
while queue:
    loc,dis,rk=queue.popleft()
    if loc==target:
        print(dis)
        flag=False
        break
    for dx,dy in [(-1,0),(1,0),(0,-1),(0,1)]:
        x,y=loc[0]+dx,loc[1]+dy
        if 0<=x<R and 0<=y<C:
            if matrix[x][y]!='#':
                if not visited[x][y][rk]:
                	queue.append(((x,y),dis+1,rk))
                	visited[x][y][rk]=True
            else:
                if rk>0 and not visited[x][y][rk-1]:
                    queue.append(((x,y),dis+1,rk-1))
                    visited[x][y][rk-1]=True
if flag:
    print(-1)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260311164111477](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260311164111477.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2025spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

每天做选做题,学到了一些东西

OJ04093:倒排索引查询

刚知道集合还有交集（&）并集（|）补集（-）这样方便的用法，比第一次做这道题时对所有文件一个个查询快了三倍。

```python
import sys
data=list(map(int,sys.stdin.read().split()))
index=0
N=data[index]
index+=1
d=[]
for _ in range(N):
    l=data[index]
    index+=1
    d.append(data[index:index+l])
    index+=l
M=data[index]
index+=1
for _ in range(M):
    query=data[index:index+N]
    index+=N
    in_set=[]
    ex_set=set()
    for i in range(N):
        if query[i]==1:
            in_set.append(set(d[i]))
        elif query[i]==-1:
            ex_set.update(d[i])
    ans=set()
    if in_set:
        ans=in_set[0]
        for ins in in_set[1:]:
            ans=ans&ins #
        ans=ans-ex_set  #
    if ans:
        print(*sorted(list(ans)))
    else:
        print('NOT FOUND')
```

## KMP算法

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



