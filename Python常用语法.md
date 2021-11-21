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

