建哈夫曼树：运用小顶堆，创建节点时定义用权值作为比较大小的依据。依次合并最小的两个节点，把两个子节点权值的加和作为新节点的权值，直到只剩下一个根节点。

计算带权外部路径长度总和：运用dfs，到达叶子节点时，把（权值*层数）加到答案中。如果存在左或右子节点，搜索（子节点，层数+1）

```python
import heapq
class Node:
    def __init__(self,weight=0):
        self.weight=weight
        self.left=None
        self.right=None
    def __lt__(self, other):
        return self.weight<=other.weight
def build_huffman_tree(nums):
    heap=[]
    for num in nums:
        heapq.heappush(heap,Node(num))
    while len(heap)>1:
        left=heapq.heappop(heap)
        right=heapq.heappop(heap)
        merged=Node(left.weight+right.weight)
        merged.left=left
        merged.right=right
        heapq.heappush(heap,merged)
    return heap[0]
def dfs(node,layer):
    if not node.left and not node.right:
        ans[0]+=layer*node.weight
        return
    if node.left:
        dfs(node.left,layer+1)
    if node.right:
        dfs(node.right,layer+1)
n=int(input())
root=build_huffman_tree(list(map(int,input().split())))
ans=[0]
dfs(root,0)
print(ans[0])
```

