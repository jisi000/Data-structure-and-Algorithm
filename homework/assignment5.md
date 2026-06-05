# DSA Assignment #5: 20260401 cs201 Mock Exam

*Updated 2026-04-01 15:20 GMT+8*
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

### E02039: 反反复复	

matrix, http://cs101.openjudge.cn/practice/02039/

思路：按行分割输入的字符串，再按列读取，从头读取与从尾读取交替进行



代码：

```python
ans=''
n=int(input())
s=input()
l=[]
t=len(s)//n
for i in range(t):
    l.append(s[i*n:(i+1)*n])
for j in range(n):
    re=j
    for i in range(t):
        ans=ans+l[i][re]
        re=-1-re
print(ans)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260403115542989](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260403115542989.png)



### E02092: Grandpa is Famous	

implementation, http://cs101.openjudge.cn/practice/02092/


思路：先用字典存储每个选手的上榜次数，再翻转列表，把上榜次数作为键，对应的选手们的列表作为值，查询第二大的键



代码：

```python
from collections import defaultdict
while True:
    n,m=map(int,input().split())
    if n==m==0:
        break
    d=defaultdict(int)
    for _ in range(n):
        line=list(map(int,input().split()))
        for num in line:
            d[num]+=1
    times=sorted(d.values(),reverse=True)
    t=times[1]
    l=sorted(k for k,v in d.items() if v==t)
    print(*l)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260403115615753](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260403115615753.png)



### M02774: 木材加工	

binary search, http://cs101.openjudge.cn/practice/02774/

思路：二分查找。运用左闭右开，因此最终的答案为hi-1。check函数x=0的情况要提前return True以免把0作为除数。



代码：

```python
N,K=map(int,input().split())
woods=[]
for _ in range(N):
    woods.append(int(input()))
def check(x):
    if x==0:
        return True
    count=0
    for wood in woods:
        count+=wood//x
    return count>=K
lo,hi=0,max(woods)
while lo<hi:
    mid=(lo+hi)//2
    if check(mid):
        lo=mid+1
    else:
        hi=mid
print(hi-1)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260403115705586](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260403115705586.png)



### M04077: 出栈序列统计

dp, dfs, math, http://cs101.openjudge.cn/practice/04077/

思路：已知出栈序列数量是catalan数，按照公式计算即可。



代码：

```python
n=int(input())
re=1
for i in range(2,n+1):
    re=(4*i-2)*re//(i+1)
print(re)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260403115847576](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260403115847576.png)



### M30637: 合法出栈序列pub

stack, http://cs101.openjudge.cn/practice/M30637/

思路：当一个字符出栈时，说明它前面的字符都入栈了，如果该字符在之前已入栈，那它必须是栈顶元素。根据这个逻辑判断即可。需要注意当待判断字符串中有重复元素就直接返回‘NO’，防止代码报错。



代码

```python
s0=input()
def judge():
    if len(s)!=len(s0):
        return 'NO'
    stack=[]
    lx=0
    visited=set()
    for ch in s:
        if ch in visited:
            return 'NO'
        visited.add(ch)
        if ch not in s0:
            return 'NO'
        idx=s0.index(ch)
        if idx<lx:
            re=stack.pop() if stack else ''
            if ch!=re:
                return 'NO'
        else:
            for c in s0[lx:idx]:
                stack.append(c)
            lx=idx+1
    return 'YES'
while True:
    try:
        s=input()
        print(judge())
    except EOFError:
        break
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260403110756216](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260403110756216.png)



### T30102:完美交易窗口

monotonic stack, http://cs101.openjudge.cn/practice/T30102/

思路：用两个单调栈，一个单减，一个严格单增。对每个时刻，假设以它为卖出点，寻找最长的完美窗口，这样可以做到O(n)的时间复杂度。先从单减栈中找到前面第一个比他大的数字l，完美窗口只能在l后面找，然后找到这个区间中最小的数字，这个数字是严格单增栈中l后面的第一个索引。



代码

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



<mark>（至少包含有"Accepted"）</mark>

![image-20260403113453408](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260403113453408.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

这次月考有事没有现场参加，事后自己计时做了一下。前四题都做过很快就做完了。第五题思路比较清晰，但debug花了一段时间。第六题一开始参照以前做的单调栈题目思路，只考虑使用一个单调栈，思路一直很混乱，到时间了也没做出来，算是AC5。然后看群里的讨论，发现可以使用双单调栈解决，感觉自己在这里思路还是局限了，计划找一些双单调栈的题目练习一下。



