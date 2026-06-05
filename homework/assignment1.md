# Assignment #1: OOP

*Updated 2026-03-03 11:25 GMT+8*
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

### E27653: Fraction类

OOP, http://cs101.openjudge.cn/pctbook/E27653/

> 主要是练习面向对象编程写法，这样力扣题目，笔试都没有问题了。机考时候，不是必须OOP，能AC就可以。
>

思路：先计算分母的最小公倍数，将两个分数通分，然后做加法，再约分。

学习了计算最大公约数和最小公倍数的程序写法。

代码：

```python
def gcd(a,b):
    while b!=0:
        a,b=b,a%b
    return abs(a)
def lcm(a,b):
    return a*b//gcd(a,b)
def reduction(a,b):
    g=gcd(a,b)
    return a//g,b//g
n1,n2,n3,n4=map(int,input().split())
c=lcm(n2,n4)
b1,b2=c//n2,c//n4
n=n1*b1+n3*b2
n_n,n_d=reduction(n,c)
print(f'{n_n}/{n_d}')
```

OOP版（可能我写的不好，好像这样很繁琐）

```python
class Fraction:
    def __init__(self,num=0,den=1):
        self.den=den
        self.num=num
    def reduction(self):
        a, b = int(self.den), int(self.num)
        while b != 0:
            a, b = b, a % b
        check=abs(a)
        if check!=1:
            self.den//=check
            self.num//=check
class FractionAdd:
    def __init__(self,f1,f2):
        self.f1=f1
        self.f2=f2
    def gcd(self,x, y):
        a,b=x,y
        while b != 0:
            a, b = b, a % b
        return abs(a)
    def lcm(self,a, b):
        return a * b // self.gcd(a, b)
    def calculate(self):
        n1, n2, n3, n4 = self.f1.num,self.f1.den,self.f2.num,self.f2.den
        c = self.lcm(n2, n4)
        b1, b2 = c // n2, c // n4
        n = n1 * b1 + n3 * b2
        x3=Fraction(n,c)
        x3.reduction()
        print(f'{x3.num}/{x3.den}')
data=list(map(int,input().split()))
x1=Fraction(data[0],data[1])
x2=Fraction(data[2],data[3])
ad=FractionAdd(x1,x2)
ad.calculate()
```

代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260303151342120](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303151342120.png)

![image-20260303153950811](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303153950811.png)



### E190.颠倒二进制位

bit manipulation, https://leetcode.cn/problems/reverse-bits/


思路：完成整数转二进制数、二进制数颠倒、二进制数转整数三个操作即可

代码：

利用内置函数的简洁版

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        return int(f'{n:032b}'[::-1],2)
```

运用位运算的方法

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        ans=0
        for _ in range(32):
            bit=n&1
            n//=2
            ans=ans*2+bit
        return ans
```

代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260303154358610](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303154358610.png)



### E1356.根据数字二进制下 1 的数目排序

bit manipulation, https://leetcode.cn/problems/sort-integers-by-the-number-of-1-bits/

思路：先排‘1’的数量，再排数字大小。先利用了将二进制数转为‘0’‘1’列表再求和的方法得到‘1’的数量，发现很慢，然后利用了内置函数

bit_count()

代码：

```python
class Solution:
    def sortByBits(self, arr: List[int]) -> List[int]:
        arr.sort(key=lambda x:(sum(list(map(int,list(bin(x)[2:])))),x))
        return arr
```

```python
class Solution:
    def sortByBits(self, arr: List[int]) -> List[int]:
        return sorted(arr,key=lambda x:(x.bit_count(),x))
```

代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260303162014415](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303162014415.png)

![image-20260303162322371](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303162322371.png)

### M27300: 模型整理

sortings, AI, http://cs101.openjudge.cn/pctbook/M27300/



思路：

利用一次数字排序，一次字符串排序

代码：

```python
from collections import defaultdict
n=int(input())
d=defaultdict(list)
for _ in range(n):
    a,b=input().split('-')
    d[a].append(b)
for k,v in d.items():
    h=[]
    for s in v:
        num=float(s[:-1])
        if s[-1]=='B':
            num*=1000
        h.append((s,num))
    h.sort(key=lambda x:x[1])
    d[k]=[c[0] for c in h]
name=sorted(d.keys())
for na in name:
    print(f'{na}: ',end='')
    print(', '.join(d[na]))
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260303154740434](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303154740434.png)



### M1536.排布二进制网格的最少交换次数

greedy, matrix, https://leetcode.cn/problems/minimum-swaps-to-arrange-a-binary-grid/



思路：如果第i行不满足条件，找到其下第一个满足条件的行，把它翻到最上面，翻转次数是j-i。



代码：

```python
class Solution:
    def minSwaps(self, grid: List[List[int]]) -> int:
        ans=0
        n=len(grid)
        pos=[-1]*n
        for i in range(n):
            for j in range(n-1,-1,-1):
                if grid[i][j]==1:
                    pos[i]=j
                    break
        for i in range(n-1):
            if pos[i]<=i:
                continue
            for j in range(i+1,n):
                if pos[j]<=i:
                    ans+=j-i
                    for x in range(j,i,-1):
                        pos[x],pos[x-1]=pos[x-1],pos[x]
                    break
            if pos[i]>i:
                return -1
        return ans
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260303170505784](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303170505784.png)

### T20052:最大点数（同2048规则）

dfs, matrices, http://cs101.openjudge.cn/pctbook/T20052/

思路：根据题面的模板进行上下左右的移动操作。运用dfs，利用re统计操作次数，每次搜索re+1，达到p停止搜索。每一次都尝试上下左右四个方向。



代码：

```python
def left(board):
    changed=False
    while True:
        c=False
        for i in range(m):
            for j in range(n-1,0,-1):
                if board[i][j]>0 and board[i][j-1]==0:
                    board[i][j],board[i][j-1]=0,board[i][j]
                    c=True
        changed=c
        if not c:
            break
    for i in range(m):
        for j in range(n-1):
            if board[i][j]==board[i][j+1]:
                board[i][j]*=2
                board[i][j+1]=0
                changed=True
    while True:
        c=False
        for i in range(m):
            for j in range(n-1,0,-1):
                if board[i][j]>0 and board[i][j-1]==0:
                    board[i][j],board[i][j-1]=0,board[i][j]
                    c=True
        if not c:
            break
    return changed
def right(board):
    changed = False
    while True:
        c = False
        for i in range(m):
            for j in range(n-1):
                if board[i][j] > 0 and board[i][j + 1] == 0:
                    board[i][j], board[i][j + 1] = 0, board[i][j]
                    c = True
        changed = c
        if not c:
            break
    for i in range(m):
        for j in range(n-1,0,-1):
            if board[i][j] == board[i][j - 1]:
                board[i][j] *= 2
                board[i][j - 1] = 0
                changed = True
    while True:
        c = False
        for i in range(m):
            for j in range(n - 1):
                if board[i][j] > 0 and board[i][j + 1] == 0:
                    board[i][j], board[i][j + 1] = 0, board[i][j]
                    c=True
        if not c:
            break
    return changed
def up(board):
    changed=False
    while True:
        c=False
        for j in range(n):
            for i in range(m-1,0,-1):
                if board[i][j]>0 and board[i-1][j]==0:
                    board[i][j],board[i-1][j]=0,board[i][j]
                    c=True
        changed=c
        if not c:
            break
    for j in range(n):
        for i in range(m-1):
            if board[i][j]==board[i+1][j]:
                board[i][j]*=2
                board[i+1][j]=0
                changed=True
    while True:
        c=False
        for j in range(n):
            for i in range(m-1,0,-1):
                if board[i][j]>0 and board[i-1][j]==0:
                    board[i][j],board[i-1][j]=0,board[i][j]
                    c=True
        if not c:
            break
    return changed
def down(board):
    changed=False
    while True:
        c=False
        for j in range(n):
            for i in range(m-1):
                if board[i][j]>0 and board[i+1][j]==0:
                    board[i][j],board[i+1][j]=0,board[i][j]
                    c=True
        changed=c
        if not c:
            break
    for j in range(n):
        for i in range(m-1,0,-1):
            if board[i][j]==board[i-1][j]:
                board[i][j]*=2
                board[i-1][j]=0
                changed=True
    while True:
        c=False
        for j in range(n):
            for i in range(m-1):
                if board[i][j]>0 and board[i+1][j]==0:
                    board[i][j],board[i+1][j]=0,board[i][j]
                    c=True
        if not c:
            break
    return changed
import copy
def dfs(board,re):
    num = max(max(row) for row in board)
    answer[0] = max(answer[0], num)
    if re==p:
        return
    for move_func in [up, down, left, right]:
        new_board = copy.deepcopy(board)
        if move_func(new_board):
            dfs(new_board, re + 1)
m, n, p = map(int, input().split())
b = [list(map(int, input().split())) for _ in range(m)]
answer=[float('-inf')]
dfs(b,0)
print(answer[0])
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260303155507395](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260303155507395.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2025spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

跟随每日选做题学习了位运算，以下是学习笔记

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
