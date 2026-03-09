如果数组总和不能整除x，就输出n。如不然，找到不能整除x的最长前后缀，输出较长者。长度为0则输出-1

```python
t=int(input())
for _ in range(t):
    n,x=map(int,input().split())
    arr=list(map(int,input().split()))
    ans=-1
    if sum(arr)%x!=0:
        print(n)
    else:
        r=1
        sl=arr[0]
        left=0
        while r<=n-1:
            if sl%x!=0:
                left=n-r
                break
            sl+=arr[r]
            r+=1
        l=n-1
        sr=arr[-1]
        right=0
        while l>=0:
            if sr%x!=0:
                right=l
                break
            sr+=arr[l-1]
            l-=1
        ans=max(left,right)
        print(ans if ans!=0 else -1)
```

