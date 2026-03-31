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

从有序数组建树（平衡二叉树）

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

从无序数组建树（不平衡）

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

例2：CF2195E-Idiot First Search（遍历式dp）

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

## 回文串

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

## 逆序对

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

## 状态压缩 DP 

定义状态 dp[mask][i}：表示已访问城市集合为 mask（二进制数），当前位于城市 i 时的最短路径长度。 

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
