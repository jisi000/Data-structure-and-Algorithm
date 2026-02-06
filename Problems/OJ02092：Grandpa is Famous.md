先用字典存储每个选手的上榜次数，再翻转列表，把上榜次数作为键，对应的选手们的列表作为值，查询第二大的键

```python
from collections import defaultdict
while True:
    N,M=map(int,input().split())
    if N==M==0:
        break
    d={}
    for _ in range(N):
        data=list(map(int,input().split()))
        for candidate in data:
            d[candidate]=d.get(candidate,0)+1
    d_reverse=defaultdict(list)
    for key,value in d.items():
        d_reverse[value].append(key)
    s=sorted(d_reverse.keys(),reverse=True)
    print(*sorted(d_reverse[s[1]]))
```

