因为要输出成功排序或出现环的行数，因此每加一条边，就进行一次拓扑排序。

如果一轮有两个及以上节点入度为0，说明无法排序，但也不能直接返回None，需要先检查是否有环。

```python
def topological_sort(graph):
    in_degree=[0]*n
    for line in graph:
        for v in line:
            in_degree[v] += 1
    visited=[0]*n
    s=[]
    flag=True
    while len(s)<n:
        update=0
        re_degree=in_degree.copy()
        for i in range(n):
            if visited[i]==0 and re_degree[i]==0:
                s.append(i)
                visited[i]=1
                update+=1
                for j in graph[i]:
                    in_degree[j]-=1
        if update>=2:
            flag=False
        if update==0:
            break
    if len(s)!=n:
        return 1
    else:
        return ''.join([chr(x+65) for x in s]) if flag else None
def main():
    graph=[[] for _ in range(n)]
    for i in range(1,m+1):
        u, v=edges[i-1]
        a, b = ord(u) - 65, ord(v) - 65
        graph[a].append(b)
        ans=topological_sort(graph)
        if ans:
            if ans==1:
                return f'Inconsistency found after {i} relations.'
            else:
                return f'Sorted sequence determined after {i} relations: {ans}.'
    return 'Sorted sequence cannot be determined.'
while True:
    n,m=map(int,input().split())
    if n==m==0:
        break
    edges=[]
    for _ in range(m):
        edges.append(tuple(input().split('<')))
    print(main())
```

