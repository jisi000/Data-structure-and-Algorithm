维护长度为n的小顶堆，先把第一个数组全部推入堆，对剩下的m-1个数组依次叠加。

维护一个长度为n大顶堆作为辅助，将小顶堆中的数字依次与当前数组中的数相加。若大顶堆没填满便直接填充，若大顶堆已经填满，当前加和若比大顶堆最大的数小就替换，若不然后面的数必然更大，直接结束循环。

一轮循环结束后，将大顶堆取相反数复制成新的小顶堆。

```python
T=int(input())
import heapq
for _ in range(T):
    m,n=map(int,input().split())
    arr=list(map(int,input().split()))
    heap=arr[:]
    heapq.heapify(heap)
    for _ in range(m-1):
        n_arr=list(map(int,input().split()))
        n_arr.sort()
        n_heap=[]
        while heap:
            re_num=heapq.heappop(heap)
            for num in n_arr:
                n_num=re_num+num
                if len(n_heap)<n:
                    heapq.heappush(n_heap,-n_num)
                else:
                    if n_num<-n_heap[0]:
                        heapq.heappop(n_heap)
                        heapq.heappush(n_heap,-n_num)
                    else:
                        break
        heap=[-x for x in n_heap]
        heapq.heapify(heap)
    heap.sort()
    print(*heap)
```

