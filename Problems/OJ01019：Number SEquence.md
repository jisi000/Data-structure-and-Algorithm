维护一个字符串re，作为当前的Sk，从S1='1'开始。如果i大于Sk的长度，说明要查询的数字位于后面的某个Sn(n>k)串，把Sk的长度从i中减去，代表已经走过了len（Sk），还需要向后走i-len（Sk）的长度。接着更新Sk为Sk+1，重复操作。直到i小于Sk的长度，说明查询的数字就在这个串，将i作为索引输出re[i-1]

```python
t=int(input())
for _ in range(t):
    i=int(input())
    re='1'
    l=1
    while i>len(re):
        i-=len(re)
        l=l+1
        re+=str(l)
    print(re[i-1])
```

