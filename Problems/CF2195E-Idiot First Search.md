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

