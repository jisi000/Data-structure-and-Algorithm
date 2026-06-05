# DSA Assignment #4: 线性结构

*Updated 2026-03-23 22:22 GMT+8*
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

### E160.相交链表

hash table, linked list, two pinters, https://leetcode.cn/problems/intersection-of-two-linked-lists/

思路：根据相交链表的独特构造，可以这样解决问题：a一直向后走，走到末尾，就来到b的头节点，再顺着b往后走。而b走到末尾，就来到a的头节点，再顺着a往后走。这样a与b能同时到达相交节点。



代码：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]:
        a,b=headA,headB
        while a != b:
            a=a.next if a else headB
            b=b.next if b else headA
        return a
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260324131137087](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260324131137087.png)



### E206.反转链表

recursion, linked list, https://leetcode.cn/problems/reverse-linked-list/


思路：re储存了反转后链表的当前头节点。由于链表只能正向遍历，因此re是从尾到头延长的。每次暂存h的下一个节点，把h连接到re的头部。然后把re挪到头部，h挪到下一个节点。



代码：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
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



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260324132424404](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260324132424404.png)



### M234.回文链表

linked list, two pointers, https://leetcode.cn/problems/palindrome-linked-list/

<mark>请用快慢指针实现</mark> `O(1)` 空间复杂度。

思路：使用快慢指针，每次fast移动两步，slow移动一步。这样当fast移到末尾时，slow就恰好在中间。回文序列分为轴对称和中心对称，中心对称的情况需要把slow再挪一步。

在移动slow的同时，把前半部分链表反转，方法与 E206.反转链表 相同。

把slow和反转后的链表re依次比较。



代码：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def isPalindrome(self, head: Optional[ListNode]) -> bool:
        slow,fast=head,head
        re=None
        while fast and fast.next:
            fast=fast.next.next
            nxt=slow.next
            slow.next=re
            re=slow
            slow=nxt
        if fast:
            slow=slow.next
        while slow and re:
            if slow.val!=re.val:
                return False
            slow=slow.next
            re=re.next
        if slow or re:
            return False
        return True
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260324134249103](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260324134249103.png)



### M24591:中序表达式转后序表达式

stack, http://cs101.openjudge.cn/practice/24591/

思路：先解决输入数据整数、小数、符号混杂的问题。利用数字都被符号隔开的性质，将每个数字合并成一个小字符串存储，符号都单独存储。

分别解决运算顺序问题：

**括号里的优先运算：**使用stack，当遇到')'，将最近的'('之后的部分pop出去，先转为后序表达式，然后将这个后序表达式合并为一个字符串存回来。

**从左到右，先乘除后加减：**定义trans函数，从左到右寻找第一个运算符号，

如果是乘除，直接将符号及其左右的部分（数字或已经合并为字符串的后序表达式）转为一个合并的后序表达式，再递归处理新的表达式。

如果是加减，分类讨论：如果后面的第一个符号也是加减或后面没有符号，同上。

如果后面第一个符号为乘除，先把该符号及左右部分转为后序表达式，再递归处理。

递归的结束条件是式子中不存在单独的符号（符号都以合并成字符串的后序表达式存在）



代码：

```python
def trans(s):
    ans=s
    for i in range(len(s)):
        if s[i] in ['*','/']:
            ans=trans([' '.join(s[:i]+[s[i+1],s[i]])]+s[i+2:])
            break
        elif s[i] in ['+','-']:
            if i+2<len(s) and s[i+2] in ['*','/']:
                ans=trans(s[:i+1]+[' '.join([s[i+1],s[i+3],s[i+2]])]+s[i+4:])
            else:
                ans=trans([' '.join(s[:i]+[s[i+1],s[i]])]+s[i+2:])
            break
    return ans
n=int(input())
for _ in range(n):
    stack=[]
    inp=input()
    n_inp=[]
    stu=0 if inp[0].isdigit() else 1
    re=''
    for ch in inp:
        if ch.isdigit() or ch=='.':
            if stu==0:
                re+=ch
            else:
                if re:
                    n_inp.append(re)
                re=ch
                stu=0
        else:
            if stu==0:
                if re:
                    n_inp.append(re)
                re=ch
                stu=1
            else:
                if re:
                    n_inp.append(re)
                re=ch
    if re:
        n_inp.append(re)
    for ch in n_inp:
        if ch==')':
            st=[]
            while stack:
                re=stack.pop()
                if re=='(':
                    break
                st=[re]+st
            stack.append(' '.join(trans(st)))
        else:
            stack.append(ch)
    back=trans(stack)
    print(*back)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260324141854223](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260324141854223.png)



### M146.LRU缓存

hash table, doubly-linked list, https://leetcode.cn/problems/lru-cache/

思路：先建立一个哨兵节点，将所有节点连成一个环。定义add和delete操作，add的新节点都插入到哨兵节点后面，delete可以发生在任何位置，只需要把待删除节点的前后节点连接即可。d字典：键值映射到节点

get操作：如果能get到，就把该节点delete再重新add一次，以将其提到最前，表示“使用”

put操作：如果已经有该节点，就更新值，同时进行增删操作，表示“使用”。

如果不存在，就add节点，然后检查d字典长度，如果超了，就删除哨兵节点前面的节点，这个就是最久未使用的节点。



代码

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
        self.d={}
    def delete(self,node):
        node.next.last=node.last
        node.last.next=node.next
    def add(self,node):
        nxt=self.sen.next
        self.sen.next=node
        node.next=nxt
        node.last=self.sen
        nxt.last=node
    
    def get(self, key: int) -> int:
        if key in self.d:
            node=self.d[key]
            self.delete(node)
            self.add(node)
            return node.val
        return -1
	def put(self, key: int, value: int) -> None:
        if key in self.d:
            node=self.d[key]
            self.delete(node)
            self.add(node)
            node.val=value
            return
        self.d[key]=Node(value,key)
        self.add(self.d[key])
        if len(self.d)>self.c:
            del_node=self.sen.last
            self.delete(del_node)
            del self.d[del_node.key]


# Your LRUCache object will be instantiated and called as such:
# obj = LRUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260324134439576](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260324134439576.png)



### P2698 [USACO12MAR] Flowerpot S

monotonic queue, https://www.luogu.com.cn/problem/P2698

思路：将水滴按照(x,y)元组来排序。遍历水滴，维护y值的单增队列和单减队列。每次以当前位置为右边界，寻找符合要求的左边界。



代码

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



<mark>（至少包含有"Accepted"）</mark>

![image-20260324154842875](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260324154842875.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

根据作业总结了链表相关的知识点

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



跟随每日选做题复习了二分查找

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



