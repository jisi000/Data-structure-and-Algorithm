# DSA Assignment #7: 🌲（2/3）

*Updated 2026-04-09 15:45 GMT+8*
 *Compiled by <mark>孙婧斯、生命科学学院e'eee</mark> (2026 Spring)*



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

### M297.二叉树的序列化与反序列化

dfs, bfs, https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/

思路：使用前序遍历。为了区分节点，在序列化时把所有的叶子节点都用None封装。反序列化时，递归的结束条件就是节点为None。



代码：  

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        def pre(node):
            if not node:
                l.append('null')
                return
            l.append(str(node.val))
            pre(node.left)
            pre(node.right)
        l=[]
        pre(root)
        return '*'.join(l)
    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        l=list(int(val) if val!='null' else None for val in list(data.split('*')))
        idx=[0]
        def build():
            node=None if l[idx[0]] is None else TreeNode(l[idx[0]])
            idx[0]+=1
            if node:
                node.left=build()
                node.right=build()
            return node
        return build()
# Your Codec object will be instantiated and called as such:
# ser = Codec()
# deser = Codec()
# ans = deser.deserialize(ser.serialize(root))
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260412203135879](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260412203135879.png)



### M129.求根节点到叶节点数字之和

dfs, https://leetcode.cn/problems/sum-root-to-leaf-numbers/


思路：遍历到一个节点，把此前的数字×10，再加上当前数字。如果该节点没有子节点，把这个值加到答案中，如果有子节点就递归遍历。

代码：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sumNumbers(self, root: Optional[TreeNode]) -> int:
        ans=[0]
        def search(node,num):
            if not node.left and not node.right:
                ans[0]+=num*10+node.val
                return
            if node.left:
                search(node.left,num*10+node.val)
            if node.right:
                search(node.right,num*10+node.val)
        search(root,0)
        return ans[0]
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260412203851208](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260412203851208.png)



### M22158:根据二叉树前中序序列建树

tree, http://cs101.openjudge.cn/practice/22158/



思路：前序遍历的第一个字母是根节点，在中序遍历中找到根节点的位置lo，那么左子树即为用pre[1:lo+1]和mid[:lo]建的树，右子树即为用pre[lo+1:]和mid[lo+1:]建的树。如此可以用递归思路建树。

建完树进行后序遍历即可

代码：

```python
class TreeNode:
    def __init__(self,val=None,left=None,right=None):
        self.val=val
        self.left=left
        self.right=right
def generate_tree(pre,mid):
    if not pre:
        return None
    root=TreeNode(pre[0])
    lo=-1
    for i in range(len(mid)):
        if mid[i]==root.val:
            lo=i
    root.left=generate_tree(pre[1:lo+1],mid[:lo])
    root.right=generate_tree(pre[lo+1:],mid[lo+1:])
    return root
def back_order(node):
    if node:
        back_order(node.left)
        back_order(node.right)
        ans.append(node.val)
while True:
    try:
        pre_order=input()
        mid_order=input()
        ans=[]
        tree=generate_tree(pre_order,mid_order)
        back_order(tree)
        print(''.join(ans))
    except EOFError:
        break
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260412204008310](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260412204008310.png)



### M24729:括号嵌套树

dfs, stack, http://cs101.openjudge.cn/practice/24729/



思路：括号嵌套树本身的字母顺序即为前序遍历

使用stack得到后序遍历：当遇到反括号时，把子树pop出来并正向排列好，遇到正括号说明前面就是根节点，把子树放到根节点前，合并成一个字符串放回stack。

代码：

```python
pre=[]
stack=[]
for ch in input():
    if ch==')':
        t=''
        while stack:
            re=stack.pop()
            if re=='(':
                t=t+stack.pop()
                break
            if re!=',':
                t=re+t
        stack.append(t)
    else:
        if ch!=',':
            stack.append(ch)
            if ch!='(':
                pre.append(ch)
print(''.join(pre))
print(''.join(stack))
```



代码运行截图 <mark>（至少包含有"Accepted"）</mark>

![image-20260412204503494](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260412204503494.png)



### M01577: Falling Leaves

tree, http://cs101.openjudge.cn/practice/01577/



思路：按二叉搜索树比当前节点值小的插入到左子树，比当前节点值大的插入到右子树的规则插入，然后前序遍历



代码

```python
class TreeNode:
    def __init__(self,val):
        self.val=val
        self.left=None
        self.right=None
import sys
data=list(sys.stdin.read().split())
trees=[]
tree=[]
for l in data:
    if l=='*' or l=='$':
        trees.append(tree[::-1])
        tree=[]
        continue
    tree.append(l)
def insert(c,node):
    if c<node.val:
        if node.left:
            insert(c,node.left)
        else:
            node.left=TreeNode(c)
    else:
        if node.right:
            insert(c,node.right)
        else:
            node.right=TreeNode(c)
def pre(node):
    if node:
        ans.append(node.val)
        pre(node.left)
        pre(node.right)
for tree in trees:
    root=TreeNode(tree[0]) if tree else None
    for line in tree[1:]:
        for ch in line:
            insert(ch,root)
    ans=[]
    pre(root)
    print(''.join(ans))
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260412205135480](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260412205135480.png)



### 1843D. Apple Tree

 Combinatorics, dfs and similar, dp, math, trees, 1200,  https://codeforces.com/problemset/problem/1843/D

思路：因为不确定边的方向，先用邻接表存储，然后从根节点1开始递归。查找每个节点能到的的叶子节点数，记录在nums中。递归终止条件是邻接表中只有父节点，此时它本身是叶子节点，把nums[val]标为1。其余节点的数值是其子节点的数值之和。组合的方法数就是nums[x]*nums[y]



代码

```python
import sys
sys.setrecursionlimit(1 << 25)
t=int(input())
def build(father,val):
    if len(graph[val])==1 and father in graph[val]:
        nums[val]=1
        return
    re=0
    for child in graph[val]:
        if child!=father:
            build(val,child)
            re+=nums[child]
    nums[val]=re
for _ in range(t):
    n=int(input())
    graph={i:set() for i in range(1,n+1)}
    for _ in range(n-1):
        r,c=map(int,input().split())
        graph[r].add(c)
        graph[c].add(r)
    nums=[0]*(n+1)
    build(0,1)
    g=int(input())
    for _ in range(g):
        x,y=map(int,(input().split()))
        print(nums[x]*nums[y])
```



<mark>（至少包含有"Accepted"）</mark>

![image-20260412203452397](C:\Users\嵇兕\AppData\Roaming\Typora\typora-user-images\image-20260412203452397.png)



## 2. 学习总结和个人收获

<mark>如果发现作业题目相对简单，有否寻找额外的练习题目，如“数算2026spring每日选做”、LeetCode、Codeforces、洛谷等网站上的题目。</mark>

学习了一些特殊的树

#### 二叉搜索树BST

对二叉搜索树的每个节点而言，它左子节点比它小，右子节点比它大

##### 从有序数组建树（平衡二叉树）

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

##### 从无序数组建树（不平衡）

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

##### 查找第K小的元素

二叉搜索树的中序遍历就是正序的序列。可以直接用dfs中序遍历，按照索引查询，但这样需要遍历完整个树，时间长。

使用中序遍历的迭代写法可以在找到时就终止遍历，缩短时间。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        i=1
        stack=[]
        while stack or root:
            while root:
                stack.append(root)
                root=root.left
            node=stack.pop()
            if i==k:
                return node.val
            i+=1
            root=node.right
```



#### 哈夫曼编码树

哈夫曼编码树是一种二叉树，根据字符使用频率(权值)生成，能根据字符出现频率为字符串编码，使总的编码长度最小。

##### 规则

确保哈夫曼编码树唯一性的生成规则是：

选取最小的两个节点合并时，节点比大小的规则是:

1) 权值小的节点算小。权值相同的两个节点，字符集里最小字符小的，算小。例如 （{'c','k'},12) 和 ({'b','z'},12)，后者小。
2) 合并两个节点时，小的节点必须作为左子节点
3) 连接左子节点的边代表0,连接右子节点的边代表1

节点形式：每个节点都有变量和权值。叶子节点的变量是一个字母，权值是这个字母的权值。其余的各级根节点的权值是所有子节点的权值的总和，变量是一个子节点字母组成的有序字符串。

##### 生成

```python
import heapq
class Node:
    def __init__(self,val='',weight=0):
        self.val=val
        self.weight=weight
        self.left=None
        self.right=None
    def __lt__(self,other):
        if self.weight==other.weight:
            return self.val[0]<other.val[0]
        return self.weight<other.weight
heap=[]
n=int(input())
for _ in range(n):
    st,fre=input().split()
    node=Node(st,int(fre))
    heapq.heappush(heap,node)
while len(heap)>1:
    node1=heapq.heappop(heap)
    node2=heapq.heappop(heap)
    n_st=''.join(sorted(list(node1.val+node2.val)))
    n_node=Node(n_st,node1.weight+node2.weight)
    n_node.left=node1
    n_node.right=node2
    heapq.heappush(heap,n_node)
root=heap[0]
```

##### 编码（字符串->01串）

```python
def encode(s):
    ans=''
    re=root
    for ch in s:
        while re:
            if ch in re.left.val:
                ans+='0'
                re=re.left
            elif ch in re.right.val:
                ans+='1'
                re=re.right
            if not re.left and not re.right:
                break
        re=root
    return ans
```

##### 解码（01串->字符串）

```python
def decode(s):
    ans=''
    re=root
    for ch in s:
            if ch=='0':
                re=re.left
            elif ch=='1':
                re=re.right
            if not re.left and not re.right:
                ans+=re.val
                re=root
    return ans
```

#### 括号嵌套树

括号嵌套树是一个用括号体现节点间的关系的字符串，其中二叉树会有*表示的空节点，遍历时需要去掉。

##### 前序遍历

括号嵌套树的前序遍历，就是字符串中节点值的顺序

##### 中序遍历

只有二叉树能这样遍历  OJ27637:括号嵌套二叉树

```python
n=int(input())
for _ in range(n):
    pre = []
    stack = []
    for ch in input():
        if ch==')': #二叉树已知子节点数目，所以直接pop()
            r=stack.pop()
            stack.pop()
            l=stack.pop()
            stack.pop()
            root=stack.pop()
            stack.append(l+root+r)
        else:
            stack.append(ch)
            if ch not in ['(',',']:
                pre.append(ch)
    pres=''.join(pre).replace('*','')
    mid=''.join(stack).replace('*','')
    print(pres)
    print(mid)
```

##### 后序遍历

多叉树和二叉树都可以这样遍历  OJ24729:括号嵌套树

```python
pre=[]
stack=[]
for ch in input():
    if ch==')':
        t=''
        while stack: #多叉树不知道子节点数目，所以需要while循环
            re=stack.pop()
            if re=='(':
                t=t+stack.pop()
                break
            if re!=',':
                t=re+t
        stack.append(t)
    else:
        if ch!=',':
            stack.append(ch)
            if ch!='(':
                pre.append(ch)
print(''.join(pre))
print(''.join(stack))
```





