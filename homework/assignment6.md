# DSA Assignment #6: 🌲（1/3）

*Updated 2026-04-05 21:54 GMT+8*
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

### E94.二叉树的中序遍历

dfs, stack, https://leetcode.cn/problems/binary-tree-inorder-traversal/

思路：中序遍历是指左子树-当前节点-右子树的顺序。遍历到节点时，先递归左节点，然后把节点值加入列表，再递归右节点。

代码：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        l=[]
        def dfs(lo):
            if lo:
                dfs(lo.left)
                l.append(lo.val)
                dfs(lo.right)
        dfs(root)
        return l
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260406111420211](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260406111420211.png)



### E108.将有序数组转换为二叉搜索树

https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/


思路：将数组中间的数作为根节点的值，前面的部分放入左子树，后面的部分放入右子树，左右子树由递归得到。

代码：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        mid=len(nums)//2
        root=TreeNode(nums[mid])
        root.left=self.sortedArrayToBST(nums[:mid])
        root.right=self.sortedArrayToBST(nums[mid+1:])
        return root
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260406110358790](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260406110358790.png)



### M102.二叉树的层序遍历

bfs, https://leetcode.cn/problems/binary-tree-level-order-traversal/

思路：使用双端队列，让先进队的节点先记录节点值。将层数随节点一并存储，将节点值存储到ans列表对应的层中。

代码：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
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



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260406110212844](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260406110212844.png)



### M1123.最深叶节点的最近公共祖先

dfs, https://leetcode.cn/problems/lowest-common-ancestor-of-deepest-leaves/

思路：先算出最大深度，然后dfs。如果当前节点深度等于最大深度，那么返回当前节点，没找到就返回None，这样有最深叶节点的地方会向上传递一个True的信号。从根节点开始对左右子树搜索，如果两边都有True的信号，说明两边都有最深叶节点，需要返回当前节点。否则返回有信号的一侧。

代码：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def lcaDeepestLeaves(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        deepth=[0]
        def get_deepth(node,d):
            if not node.left and not node.right:
                deepth[0]=max(deepth[0],d)
                return
            if node.left:
                get_deepth(node.left,d+1)
            if node.right:
                get_deepth(node.right,d+1)
        get_deepth(root,0)
        def dfs(node,d):
            if d==deepth[0]:
                return node
            l,r=None,None
            if node.left:
                l=dfs(node.left,d+1)
            if node.right:
                r=dfs(node.right,d+1)
            if l and r:
                return node
            return l or r
        return dfs(root,0)
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260406105001496](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260406105001496.png)



### M07161: 森林的带度数层次序列存储

tree, http://cs101.openjudge.cn/practice/07161/

思路：先用bfs建树，然后递归进行后序遍历。



代码

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



<mark>（至少包含有"Accepted"）</mark>

![image-20260406110114194](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260406110114194.png)



### M27928: 遍历树

 adjacency list, dfs, http://cs101.openjudge.cn/practice/27928/

思路：由于节点值互不相同，可以用邻接表存储树。

遍历节点时，先递归遍历比自身小的子节点，然后输出该节点的值，再递归遍历比自身大的子节点。

统一存储所有子节点，找到不在子节点集合中的根节点，从根节点开始遍历。

代码

```python
import bisect
d={}
child=set()
n=int(input())
for _ in range(n):
    line=list(map(int,input().split()))
    child.update(line[1:])
    d[line[0]]=line[1:]
root=-1
for num in d:
    if num not in child:
        root=num
def traversal(node):
    children=d[node]
    children.sort()
    lo=bisect.bisect_right(children,node)
    for m in children[:lo]:
        traversal(m)
    print(node)
    for m in children[lo:]:
        traversal(m)
traversal(root)
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260406105716545](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260406105716545.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

做完作业感觉树的构建和遍历都只是bfs或者dfs而已，其实树是一个很简单好用的数据结构。

最近选做题有一些有难度的dp题目，是上学期遇到的土豪购物、打家劫舍的进阶版

环形打家劫舍

LC3892.产生至少K个峰值的最小操作次数

```python
class Solution:
    def solve(self,a,k):
        n=len(a)
        dp=[[0]*n for _ in range(k+1)]
        for i in range(1,k+1):
            dp[i][0]=dp[i][1]=float('inf')
        ops=[max(max(a[i-1],a[i+1])-a[i]+1,0) for i in range(1,n-1)]
        for left in range(1,k+1):
            for i in range(1,n-1):
                not_choose=dp[left][i]
                choose=dp[left-1][i-1]+ops[i-1]
                dp[left][i+1]=min(not_choose,choose)
        return dp[k][n-1]
    def minOperations(self, nums: list[int], k: int) -> int:
        n=len(nums)
        if k>n//2:
            return -1
        cnt=0
        for i in range(n):
            if nums[i-1]<nums[i]>nums[(i+1)%n]:
                cnt+=1
        if cnt>=k:
            return 0
        ans1=self.solve([nums[-1]]+nums,k)
        ans2=self.solve(nums+[nums[0]],k)
        return min(ans1,ans2)
```

可以跳过两次的土豪购物

LC3418.机器人可以获得的最大金币数

因为可以“感化”两次，因此需要三个dp数组，代表“感化”0次，1次，2次的最大金币数。

```python
class Solution:
    def maximumAmount(self, coins: List[List[int]]) -> int:
        r,c=len(coins),len(coins[0])
        dp0=[[float('-inf')]*c for _ in range(r)]
        dp1=[[float('-inf')]*c for _ in range(r)]
        dp2=[[float('-inf')]*c for _ in range(r)]
        if coins[0][0]<0:
            dp0[0][0]=coins[0][0]
            dp1[0][0]=dp2[0][0]=0
        else:
            dp0[0][0]=dp1[0][0]=dp2[0][0]=coins[0][0]
        for i in range(1,r):
            dp0[i][0]=dp0[i-1][0]+coins[i][0]
            if coins[i][0]<0:
                dp1[i][0]=max(dp1[i-1][0]+coins[i][0],dp0[i-1][0])
                dp2[i][0]=max(dp2[i-1][0]+coins[i][0],dp1[i-1][0])
            else:
                dp1[i][0]=dp1[i-1][0]+coins[i][0]
                dp2[i][0]=dp2[i-1][0]+coins[i][0]
        for j in range(1,c):
            dp0[0][j]=dp0[0][j-1]+coins[0][j]
            if coins[0][j]<0:
                dp1[0][j]=max(dp1[0][j-1]+coins[0][j],dp0[0][j-1])
                dp2[0][j]=max(dp2[0][j-1]+coins[0][j],dp1[0][j-1])
            else:
                dp1[0][j]=dp1[0][j-1]+coins[0][j]
                dp2[0][j]=dp2[0][j-1]+coins[0][j]
        for i in range(1,r):
            for j in range(1,c):
                dp0[i][j]=max(dp0[i-1][j],dp0[i][j-1])+coins[i][j]
                if coins[i][j]<0:
                    dp1[i][j]=max(dp0[i-1][j],dp0[i][j-1],dp1[i-1][j]+coins[i][j],dp1[i][j-1]+coins[i][j])
                    dp2[i][j]=max(dp1[i-1][j],dp1[i][j-1],dp2[i-1][j]+coins[i][j],dp2[i][j-1]+coins[i][j])
                else:
                    dp1[i][j]=max(dp1[i-1][j],dp1[i][j-1])+coins[i][j]
                    dp2[i][j]=max(dp2[i-1][j],dp2[i][j-1])+coins[i][j]
        return dp2[-1][-1]
```



总结了之前做的一些单调栈/队列的题目

## 单调队列/栈

队列和栈中往往储存的是索引而非具体的数字，这样方便各种定位操作。

这是一种便于查询某区间最大/最小值的数据结构，其单增（减）性有时也会有一定作用。

其单调性的维护方式是，对于当前的数字，把前面不符合单调性的数字都pop出去，形成保证含有当前元素的单调序列。

#### 一个单调栈

OJ27205：护林员盖房子又来了

单调递增栈，计算最大全0矩形面积。可以把它看作m个高度数组，按行依次计算。

核心就是当栈单调递增时，左边的高度都可以向右边延伸。

```python
m,n=map(int,input().split())
forest=[list(map(int,input().split())) for _ in range(m)]
height=[0]*n
ans=0
for i in range(m):
    height=[h+1 if loc==0 else 0 for h,loc in zip(height,forest[i])]
    stack=[]
    for j in range(n+1):
        he=height[j] if j<n else -1
        while stack and height[stack[-1]]>he:
            h=height[stack.pop()]
            w=j-stack[-1]-1 if stack else j
            ans=max(ans,h*w)
        stack.append(j)
print(ans)
```

LC239.滑动窗口最大值

这里虽然有两个队列，但只有一个是单调递减队列，另一个仅仅是辅助队列，维持两个队列长度相等。

核心是单减队列使得滑动窗口内最大的数永远在最左边。辅助队列用于窗口滑动。

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        from collections import deque
        queue=deque()
        l=deque()
        for num in nums[:k]:
            re=0
            while queue and queue[-1]<num:
                queue.pop()
                re+=(l.pop()+1)
            queue.append(num)
            l.append(re)
        ans=[queue[0]]
        for num in nums[k:]:
            re=0
            while queue and queue[-1]<num:
                queue.pop()
                re+=(l.pop()+1)
            queue.append(num)
            l.append(re)
            if l[0]>0:
                l[0]-=1
            else:
                l.popleft()
                queue.popleft()
            ans.append(queue[0])
        return ans
```

#### 两个单调队列/栈

**一般是一个单增，一个单减。**两个单调栈能解决更复杂的问题

OJ30102：完美交易窗口

用两个单调栈，一个单减，一个严格单增。每次以当前值为卖出点。单减栈的末端是前面第一个更大的数字l，严格单增栈中l后面的第一个索引就是这个区间的最小值。这里体现了栈中存放索引的重要性。

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

LGP2698.Flowerpot S

维护y值的单增队列和单减队列。每次以当前位置为右边界，寻找符合要求的左边界。因为左边界可能更大也可能更小，因此需要两个队列。

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

