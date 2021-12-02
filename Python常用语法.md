# Python常用语法

##### 保留小数

```python
 round(s,3))
 ('x={:.2f},y={:.2f}'.format(a,y)) 
 ("{0}+{1}=".format(n1, n2))
```

##### 输出n遍

```python
for i in range(a):
    print(str , end = "!")
```

##### 字符串最后一位

```python
var1[-1]
```

##### 倒转字符

```python
b = a[::-1]
```

##### 取字符

```python
a[1,3]
#起始位0，到n+1位
```

##### 取空格分开的

```python
a,b=map(int,input().split())
同一种类型可以用map
#a=input().split()
#n=int(a[0]) 取第一个
```

##### 产生随机数

```python
random.seed(a)  #输入一个种子，确保随机数可以一样
```

##### for循环的不同用法:

```python
for i in range(1,a+1)
for i in str(a)
```

##### 判断一个字符拆是否包含？？？

```python
str(i).count('4')>0 //boolean
```

##### 填补位

```python
str(p).rjust(6) //从右边开始补，一共6位
```

```python
a = list(map(int,input().split()))
```

a.append('hello')

//[0,1,'hello']

a.extend('hello')

//[0,1,'h','e','l','l','o']

remove

pop

clear

print(*x)

//30 7 0 85 95

s = max(a)

a.remove(s)

print(f'\n最终得分: {sum(a) / len(a):.2f}')

b = a.copy()

a.reverse()//倒转数组

t[::-1]//反向输出

```python
x = [i*2 for i in [1,2,3,4]]
[2,4,6,8]


x = [random.randint(10,99) for i in range(10) if i%2==0]
x.sort(reverse = True)
print(*x)
```

[i**2 for i in range(10) if i%2 == 0]

