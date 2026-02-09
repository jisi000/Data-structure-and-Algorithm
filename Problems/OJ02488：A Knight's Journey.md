使用dfs。需要注意题目要求输出字典序最小的答案，因此每次都从（0，0）开始搜索，在前进时，根据先列数最小，再行数最小的顺序。

```python
d=[(-1,-2),(1,-2),(-2,-1),(2,-1),(-2,1),(2,1),(-1,2),(1,2)]
def dfs(r,c):
    if len(visited)==p*q:
        return visited
    for dx,dy in d:
        x,y=r+dx,c+dy
        if 0<=x<p and 0<=y<q and (x,y) not in visited:
            visited.append((x,y))
            nxt=dfs(x,y)
            if nxt:
                return nxt
            visited.pop()
    return None
n=int(input())
for t in range(n):
    p,q=map(int,input().split())
    visited=[(0,0)]
    re=dfs(0,0)
    ans=[]
    if re:
        for xi,yi in re:
            ans.append(chr(65+yi)+f'{xi+1}')
    if t!=0:
        print()
    print(f'Scenario #{t+1}:')
    if not ans:
        print('impossible')
    else:
        print(''.join(ans))
```

