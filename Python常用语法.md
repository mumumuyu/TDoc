# Python常用语法

## 开头系列：

```python
#int
a = list(map(int,input().split()))

#string
m, n = list(input().split()) 

a,b=map(int,input().split())
同一种类型可以用map
#a=input().split()
#n=int(a[0]) 取第一个
```

### 字典：

找到+1，否则创建0

```python
for b in a:
    if str(b) in dic.keys():
        dic[str(b)] +=1 #找到符合要求的key并把其value+1
for key in dic.keys():
     if dic[key] == 0:
            dic.append(key) #便利找到value为0的key
```

### 数组:

```python
num = list(map(int,input().split()))
num.reverse() #倒转
num.sort() #分类
sum(list)
len(a)
```

## 结尾系列:

### 保留小数

```python
round(s,3)
print(("{:.0f}".format(n1)))
print(("{:.0f}+{:.2f}=".format(n1,n2)))
 ("{0}+{1}=".format(n1, n2))
print(f'{d:.2f}')

print(f'\n最终得分: {sum(a) / len(a):.2f}')
```

### 占位符

```python
str(p).rjust(6) //从右边开始补，一共6位
```

//整除

** 0.5开方

**2平方

##### 产生随机数

```python
random.seed(a)  #输入一个种子，确保随机数可以一样
```

### 字符串是否包含某个字符？？？

```python
str(i).count('4')>0 //boolean
```

#### 大小写互换

```py
def zh(s):
    if ord('A')<=ord(s)<=ord('Z'):
        b=chr(ord(s)+32)
    elif ord('a')<=ord(s)<=ord('z'):
         b=chr(ord(s)-32)
    else:
        b=s
    return b
a=list(input())
b=[]
for i in range(len(a)):
    b.append(zh(a[i]))
b=''.join(b)	//字符数组可以转字符
print(b)
```

#### append与extend

- a.append('hello')

- //[0,1,'hello'] +整个元素

- a.extend('hello')

- //[0,1,'h','e','l','l','o'] +加分隔开的字符




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
print(f'aver={sum(a) / len(a):.2f}') #a是一个数组

x = [i*2 for i in [1,2,3,4]]
[2,4,6,8]


x = [random.randint(10,99) for i in range(10) if i%2==0]
x.sort(reverse = True)
print(*x)
```

[i**2 for i in range(10) if i%2 == 0]

t =[eval(i) for i in input().split()] #不会破坏原来格式

#### 判断重复

```py
a = int(input())
counta=0
countb=0
for i in range(a):
    lista = list(map(int,(input().split(" "))))
    #set无序不重复
    if len(list(set(lista))) == len(lista): 
        counta = counta + 1
    else:
        countb = countb + 1
print('True='+str(countb)+',False='+str(counta))
```

#### lambda表达式

限制就是函数只return简单的东西

```py	
f = lambda x,y,z :x+y+z
print(str(f(1,3,5)))
```

#### eval()

执行一个字符串表达式，并返回表达式的值

```py
print(eval('3 * 7'))
#21，是数
```

```python
a = str(abs(int(input())))
a = a[::-1] //反转
lista = []
lista.extend(a) //数组一个个丢
print(*lista) //空格分开
```

#### 百元买百鸡

```python
def buy(m,n):
    for i in range(1,m//5+1):
        for j in range(1,m//3+1):
            for k in range(1,m*3+1):
                if (i+j+k == n) and 5*i+3*j+k/3==m:
                    print(str(i)+','+str(j)+','+str(k))

m, n = list(input().split()) 
m = int(m)
n = int(n)
buy(m,n)
```

#### 去掉最后一个字符

```python
s = 'abc'
s = s[:-1]
#取从第0个字符至倒数第一个字符的前一个字符
```



