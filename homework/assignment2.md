# Assignment #2: 位运算、前缀和、树状数组、归并排序 & 状态压缩

*Updated 2026-03-10 11:00 GMT+8*
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

### E868.二进制间距

bit manipulation, https://leetcode.cn/problems/binary-gap/

> 主要是练习面向对象编程写法，这样力扣题目，笔试都没有问题了。机考时候，不是必须OOP，能AC就可以。
>

思路：每次获得n末位的数字，并把n右移一位，last_i用于记录上一次‘1’出现的位置，i记录当前的位是从右数第几位。如果当前n末位的数字是‘1’，就将i-last_i更新到答案中。

代码：

```python
class Solution:
    def binaryGap(self, n: int) -> int:
        ans=0
        last_i=-1
        i=0
        while n!=0:
            bit=n%2
            n>>=1
            if bit==1:
                if last_i!=-1:
                    ans=max(ans,i-last_i)
                last_i=i
            i+=1
        return ans
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260310164707931](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260310164707931.png)



### M304.二维区域和检索 - 矩阵不可变

prefix sum, https://leetcode.cn/problems/range-sum-query-2d-immutable/


思路：存储从(0,0)到当前位置的矩阵和。存储时，每块矩阵的和=上部和+左部和-两部重合部分+这个格子的值。取用时逆推得，小矩阵和=大块矩阵和-左部和-上部和+两部重合部分。

代码：

```python
class NumMatrix:

    def __init__(self, matrix: List[List[int]]):
        r,c=len(matrix),len(matrix[0])
        self.sum_pre=[[0]*(c+1) for _ in range(r+1)]
        for i in range(r):
            for j in range(c):
                self.sum_pre[i+1][j+1]=self.sum_pre[i][j+1]+self.sum_pre[i+1][j]-self.sum_pre[i][j]+matrix[i][j]

    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        return self.sum_pre[row2+1][col2+1]-self.sum_pre[row1][col2+1]-self.sum_pre[row2+1][col1]+self.sum_pre[row1][col1]


# Your NumMatrix object will be instantiated and called as such:
# obj = NumMatrix(matrix)
# param_1 = obj.sumRegion(row1,col1,row2,col2)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260310164854214](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260310164854214.png)



### M1680.连接连续二进制数字

bit manipulation, https://leetcode.cn/problems/concatenation-of-consecutive-binary-numbers/



思路：用bit统计要左移的位数，pows记录下一个位的十进制值

要加到尾部的数到达下一个位，bit+1

代码：

```python
class Solution:
    def concatenatedBinary(self, n: int) -> int:
        ans,bit,pows=0,1,2
        for i in range(1,n+1):
            if i==pows:
                bit+=1
                pows*=2
            ans=((ans<<bit)+i)%(10**9+7)
        return ans
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260310165311542](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260310165311542.png)



### M1461.检查一个字符串是否包含所有长度为 K 的二进制子串

bit manipulation, https://leetcode.cn/problems/check-if-a-string-contains-all-binary-codes-of-size-k/



思路：所有二进制字串，即为小于1<<k的所有非负整数的二进制01串。构建一个长度为1<<k的TF列表，存储哪些数字已经存在了。在遍历过程中，如果该数字已经存在，就跳过，如果不存在就在计数器上+1，当计数达到1<<k时，返回True。否则返回False。

代码：

```python
class Solution:
    def hasAllCodes(self, s: str, k: int) -> bool:
        nums=[False]*(1<<k)
        count=0
        for i in range(len(s)-k+1):
            re=int(s[i:i+k],2)
            if nums[re]:
                continue
            nums[re]=True
            count+=1
            if count==(1<<k):
                return True
        return False
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260310165510955](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260310165510955.png)



### M30178:数字华容道（Easy Version）

merge sort, binary indexed tree, http://cs101.openjudge.cn/practice/30178/

思路：将棋盘转化为一个有序数组，计算逆序对数量。同时记录空格从底部数位于第几行（最后一行为第1行）

如果 n 是奇数：逆序数为偶树则有解

如果 n 是偶数：逆序数 + 空格从底部数的行号  为奇数则有解

代码：

merge sort计算逆序对

```python
import sys
data=list(map(int,sys.stdin.read().split()))
n=data[0]
loc=-1
arr=data[1:]
if not n&1:
    loc=n-((arr.index(0))//n)
arr.remove(0)
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
merge_sort(arr)
if n&1:
    if not count[0]&1:
        print('yes')
    else:
        print('no')
else:
    if (count[0]+loc)&1:
        print('yes')
    else:
        print('no')
```

binary indexed tree计算逆序对

```python
import sys
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
data=list(map(int,sys.stdin.read().split()))
n=data[0]
loc=-1
arr=data[1:]
if not n&1:
    loc=n-((arr.index(0))//n)
arr.remove(0)
count=0
t=BinaryIndexedTree(n*n-1)
for x in range(n*n-2,-1,-1):
    count+=t.query(arr[x]-1)
    t.update(arr[x],1)
if n&1:
    if not count&1:
        print('yes')
    else:
        print('no')
else:
    if (count+loc)&1:
        print('yes')
    else:
        print('no')
```

代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260310155302631](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260310155302631.png)

![image-20260310164435696](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260310164435696.png)

发现树状数组确实更快一些

### T30201: 旅行售货商问题

bitmask dp, http://cs101.openjudge.cn/practice/30201/

思路：dp[mask] [i] 表示当前已经访问过的城市集合为 mask，并且最后访问的城市是 i 时的最小花费。

因为旅行路径是一个环，因此可以默认从第0个城市出发。从而最后访问的城市需要加上去往城市0的花费。min_cost = min(min_cost, dp[(1 << n) - 1] [i] + cost[i] [0])

初始状态：dp[1] [0] = 0，表示从城市 0 出发，只访问了城市 0，花费为 0。

遍历所有mask，遍历可能的出发城市i，再遍历可能的到达城市j。

dp[new_mask] [j] = min(dp[new_mask] [j], dp[mask] [i] + cost[i] [j])

代码：

```python
def tsp(n, cost):
    dp = [[float('inf')] * n for _ in range(1<<n)]
    dp[1][0] = 0
    for mask in range(1,1 << n):
        for i in range(n):
            if not (mask & (1 << i)):
                continue
            if dp[mask][i] == float('inf'):
                continue
            for j in range(n):
                if mask & (1 << j):
                    continue
                new_mask = mask | (1 << j)
                dp[new_mask][j] = min(dp[new_mask][j], dp[mask][i] + cost[i][j])
    min_cost = float('inf')
    for i in range(n):
        min_cost = min(min_cost, dp[(1 << n) - 1][i] + cost[i][0])
    return min_cost
def main():
    import sys
    data = sys.stdin.read().split()
    n = int(data[0])
    cost = []
    idx = 1
    for i in range(n):
        row = list(map(int, data[idx:idx + n]))
        cost.append(row)
        idx += n
    result = tsp(n, cost)
    print(result)
if __name__ == "__main__":
    main()
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260310165755595](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260310165755595.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2025spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

**学习了Manacher算法。**

LC647.回文子串

要计算的是以每个字符为中心的最长回文半径。根据回文序列的性质，在一个大回文串中左右两边对称，因此知道以左边的字符为中心的回文半径，就能把这个半径映射到右边的对称字符。只需在边界外继续检查即可保证得到正确的回文半径，同时拓展边界。

用halfl记录回文半径，r记录当前最远边界，c记录这个边界对应的回文中心。

因为回文串有奇书长度和偶数长度，在每个字符中间插入‘#’使他们统一成奇数长度的回文串。左右插入不同的字符，防止遍历越界。

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

**做了以树为基础的一些dp**

CF2195E-Idiot First Search（遍历式dp）

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

OJ24637：宝藏二叉树（经典树形dp，一个树枝两边的节点不能同时取）

使用双dp，把树看作一个个子树，dp1记录使用子树根节点的最大值，dp2记录不使用根节点的最大值。由于是完全二叉树，根节点i和左右子节点2i+1和2i+2存在索引联系。从末端向根遍历，输出根节点在dp1和dp2中的最大值。

```python
N=int(input())
t=list(map(int,input().split()))
dp1=[0]*N
dp2=[0]*N
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

