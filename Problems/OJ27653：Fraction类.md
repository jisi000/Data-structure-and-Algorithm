先定义分数Fraction，实现约分功能，在实现分数加法

```python
class Fraction:
    def __init__(self,num=0,den=1):
        self.den=den
        self.num=num
    def gcd(self):
        a,b=int(self.den),int(self.num)
        while b!=0:
            a,b=b,a%b
        return abs(a)
    def reduction(self):
        check=self.gcd()
        if check!=1:
            self.den//=check
            self.num//=check
class FractionAdd:
    def __init__(self,f1,f2):
        self.f1=f1
        self.f2=f2
        self.c=f1.den
    def lcm(self):
        a,b=int(self.f1.den),int(self.f2.den)
        mult=a*b
        while b!=0:
            a,b=b,a%b
        return mult//abs(a)
    def get_den(self):
        a=self.f1.den
        b=self.f2.den
        a1,b1=1,1
        if a!=b:
            self.c=self.lcm()
            a1=self.c//a
            b1=self.c//b
        return a1,b1
    def add(self):
        a1,b1=self.get_den()
        return self.f1.num*a1+self.f2.num*b1
    def print_ans(self):
        addition=self.add()
        new_fra=Fraction(addition,self.c)
        new_fra.reduction()
        print(f'{new_fra.num}/{new_fra.den}')
n1,n2,n3,n4=map(int,input().split())
x1=Fraction(n1,n2)
x2=Fraction(n3,n4)
ad=FractionAdd(x1,x2)
ad.print_ans()
```

学习了最大公因数的欧几里得算法

```python
def gcd(a, b):
    while b != 0:
        a, b = b, a % b
    return abs(a)
```

由此可计算最小公倍数

```python
def lcm(a, b):
    if a == 0 or b == 0:
        return 0
    return abs(a * b) // gcd(a, b)
```

不用OOP的简洁方法

```python
def gcd(a,b):
    while b!=0:
        a,b=b,a%b
    return abs(a)
def lcm(a,b):
    return a*b//gcd(a,b)
def reduction(a,b):
    g=gcd(a,b)
    return a//g,b//g
n1,n2,n3,n4=map(int,input().split())
c=lcm(n2,n4)
b1,b2=c//n2,c//n4
n=n1*b1+n3*b2
n_n,n_d=reduction(n,c)
print(f'{n_n}/{n_d}')
```
