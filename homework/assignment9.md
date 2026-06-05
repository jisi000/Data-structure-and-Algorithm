# DSA Assignment #9: 图（1/3）

*Updated 2026-04-28 13:47 GMT+8*
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

### M28046: 词梯

bfs, http://cs101.openjudge.cn/practice/28046/

思路：由于每个单词固定4个字母，可以用#ool,f#ol,fo#l,foo#等作为索引，将相邻的单词存储到字典的同一个索引下。然后进行bfs，因为要输出路径，可以用一个visited字典存储路径中每个单词的上一个单词，这样到达终点时可以进行路径回溯。

代码：

```python
from collections import deque,defaultdict
import sys

def bfs(start,target,d):
    queue=deque([start])
    visited={start:None}
    while queue:
        re=queue.popleft()
        if re==target:
            path=[re]
            while visited[re]:
                re=visited[re]
                path.append(re)
            return path[::-1]
        for i in range(4):
            for w in d[re[:i]+'*'+re[i+1:]]:
                if w not in visited:
                    visited[w]=re
                    queue.append(w)
    return []
def main():
    data=list(sys.stdin.read().split())
    n=int(data[0])
    d=defaultdict(list)
    for i in range(n):
        word=data[i+1]
        for j in range(4):
            d[word[:j]+'*'+word[j+1:]].append(word)
    ans=bfs(data[n+1],data[n+2],d)
    if not ans:
        print('NO')
    else:
        print(*ans)
    return
if __name__=='__main__':
    main()
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260428154106825](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260428154106825.png)



### M433.最小基因变化

bfs, https://leetcode.cn/problems/minimum-genetic-mutation/


思路：如果当前基因序列只改一个位点，就能变成bank中未使用过的有效基因，就是合法的变化。定义函数判断基因变化的合法性，同时进行bfs



代码：

```python
from collections import deque
class Solution:
    def minMutation(self, startGene: str, endGene: str, bank: List[str]) -> int:
        def is_vailed(s1,s2):
            count=0
            for i in range(len(s1)):
                if s1[i]!=s2[i]:
                    count+=1
            if count==1:
                return True
            else:
                return False
        visited=[True]*len(bank)
        queue=deque()
        queue.append((startGene,0))
        while queue:
            re,dis=queue.popleft()
            if re==endGene:
                return dis
            for j in range(len(bank)):
                if visited[j] and is_vailed(re,bank[j]):
                    visited[j]=False
                    queue.append((bank[j],dis+1)) 
        return -1
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260428154239938](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260428154239938.png)



### sy382: 有向图判环 中等

Karn, dfs, Floyd-Warshall, https://sunnywhy.com/sfbj/10/3/382

思路：记录所有节点的入度，把入度为零的节点加到队列中，每次弹出节点，将其下游节点的入度-1，同时把新的入度为零的节点加入队列。如果最终全部节点的入度都归零，说明这个图可以被拆开，没有环，否则有环。



代码：

```python
from collections import defaultdict,deque
n,m=map(int,input().split())
ind=[0]*n
d=defaultdict(list)
for _ in range(m):
    u,v=map(int,input().split())
    ind[v]+=1
    d[u].append(v)
queue=deque()
visited=set()
for i in range(n):
    if ind[i]==0:
        queue.append(i)
        visited.add(i)
while queue:
    re=queue.popleft()
    for idx in d[re]:
        ind[idx]-=1
    for i in range(n):
        if i not in visited and ind[i]==0:
            queue.append(i)
            visited.add(i)
if sum(ind)>0:
    print('Yes')
else:
    print('No')
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260428155929447](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260428155929447.png)



### M909.蛇梯棋

bfs, https://leetcode.cn/problems/snakes-and-ladders/

思路：队列中存储掷骰子的次数和到达的格子编号。对当前格子编号，对编号加上1-6表示移动1-6步。把新的编号转化为矩阵的位置索引dx，dy，如果这个位置不是-1，就传送到对应的位置。

代码：

```python
from math import ceil
from collections import deque
class Solution:
    def snakesAndLadders(self, board: List[List[int]]) -> int:
        n=len(board)
        c=n*n
        visited=[False]*c
        queue=deque()
        queue.append((1,0))
        visited[0]=True
        while queue:
            lo,time=queue.popleft()
            if lo==c:
                return time
            for i in range(1,7):
                n_lo=lo+i
                if n_lo<=c:
                    a=ceil(n_lo/n)
                    b=n_lo%n
                    dx=n-a
                    if a%2==0:
                        dy=n-b if b>=1 else 0
                    else:
                        dy=b-1 if b>=1 else n-1
                    if board[dx][dy]!=-1:
                        n_lo=board[dx][dy]
                    if not visited[n_lo-1]:
                        visited[n_lo-1]=True
                        queue.append((n_lo,time+1))
        return -1
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260428171025380](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260428171025380.png)



### M28050: 骑士周游

dfs, http://cs101.openjudge.cn/practice/28050/

思路：启发式搜索（Warnsdorff 规则）：在每一步选择下一个移动位置时，优先选择**下一步可选位置最少** 的格子。
实现方法：在 dfs 中，对于当前可走的 (dx, dy) 方向，与他们未来可能移动的方向数量进行升序排序，按顺序搜索。

这个方法可以使上千ms的运行时间瞬间缩小到几十ms，效果超出预期。

代码

```python
n=int(input())
start=tuple(map(int,input().split()))
def dfs(loc,c):
    if c==n**2:
        return True
    x,y=loc
    moves=[]
    for dx,dy in d:
        a,b=x+dx,y+dy
        if 0<=a<n and 0<=b<n and not visited[a][b]:
            cnt = 0
            for ddx, ddy in d:
                aa, bb = a + ddx, b + ddy
                if 0 <= aa < n and 0 <= bb < n and not visited[aa][bb]:
                    cnt += 1
            moves.append((cnt, a, b))
    moves.sort()
    for cnt, a, b in moves:
        visited[a][b] = True
        if dfs((a, b), c + 1):
            return True
        visited[a][b] = False
    return False
d=[(2,1),(2,-1),(-2,1),(-2,-1),(1,2),(1,-2),(-1,2),(-1,-2)]
visited=[[False]*n for _ in range(n)]
visited[start[0]][start[1]]=True
if dfs(start,1):
    print('success')
else:
    print('fail')
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260428164756360](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260428164756360.png)



### T37.解数独

backtracking, hash table, https://leetcode.cn/problems/sudoku-solver/

思路：用集合记录每行、每列、每个3*3宫格已有的数字，每次找到备选数字最少的空格子，尝试所有可能数字。

实现方法一：用堆，每次弹出备选数字最少的。但是备选数字是一直在动态变化的，堆优化其实用处不大，所以此方案需要519ms

代码

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        import heapq
        set_h=[set() for _ in range(9)]
        set_s=[set() for _ in range(9)]
        set_f=[[set() for _ in range(3)] for _ in range(3)]
        blank=[]
        for i in range(9):
            for j in range(9):
                num=board[i][j]
                if num!='.':
                    num=int(num)
                    set_h[i].add(num)
                    set_s[j].add(num)
                    set_f[i//3][j//3].add(num)
                else:
                    blank.append((i,j))
        get_count=lambda i,j:9-len(set_h[i]|set_s[j]|set_f[i//3][j//3])
        heap=[(get_count(i,j),i,j) for i,j in blank]
        heapq.heapify(heap)
        def dfs():
            if not heap:
                return True
            n,i,j=heapq.heappop(heap)
            candidate={1,2,3,4,5,6,7,8,9}-(set_h[i]|set_s[j]|set_f[i//3][j//3])
            for c in candidate:
                board[i][j]=str(c)
                set_h[i].add(c)
                set_s[j].add(c)
                set_f[i//3][j//3].add(c)
                if dfs():
                    return True
                set_h[i].remove(c)
                set_s[j].remove(c)
                set_f[i//3][j//3].remove(c)
            heapq.heappush(heap,(len(candidate),i,j))
            return False
        dfs()
```

实现方法二：由于一共只有81个格子，全局遍历的时间很短，于是每次遍历全盘，找到备选数字最少的格子。同时使用状态压缩记录已有数字，用位运算取代集合运算。这样只需要38ms

代码

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



<mark>（至少包含有"Accepted"）</mark>

![image-20260428161821645](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260428161821645.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

最近趁着五一假期，做完了每日选做题，并整理了期中版的cheat sheet

https://github.com/jisi000/Data-structure-and-Algorithm/blob/main/cheat%20sheet.md



