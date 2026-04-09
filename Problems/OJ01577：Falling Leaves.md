按二叉搜索树的规则插入，然后前序遍历

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

