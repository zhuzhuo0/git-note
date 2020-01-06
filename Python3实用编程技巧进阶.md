## Python3实用编程技巧进阶

#### 2.1 如何在列表，字典，集合中根据条件筛选条件

```
# 列表过滤
# for循环方法
data = [-1,2,-3,4,-5]
res = []
for x in data:
	if x >= 0:
		res.append(x)
print(res)
# 列表解析
[x for x in data if x >= 0]
# filter函数
filter(lambda x:x >= 0, data)

# 创建随机数列表
from random import randint
l = [randint(-10,10) for _ in range(10)]  -10-10之间10个随机数 没有用到的变量建议用_表示
# 列表解析
[x for x in l if x >= 0]
# filter函数
g = filter(lambda x: x >= 0,l) 返回生成器对象
next(g)  // 单个取出
// 取出列表的形式，通过list构造器生成结果列表
list(g)
结果：[5,0,3,4,5...]

# 字典解析
# 创建一个20个人的班级，键:student1...  值:50到100之间的随机整数
d = {'student%i' % i:randint(50,100) for i in range(1,21)}
# 查找90分以上的学生
# 字典解析
{k:v for k,v in d.items() if v >= 90}
# filter函数
g = filter(lambda item:item[1] >= 90,d.items())  返回生成器对象
# 把结果传给列表构造器
list(g) // 每次传给构造器之后，生成器对象就被消耗了，再次使用需要重新生成
# 结果：[('student1',93),('student5',98)]
# 把结果传给列表构造器
dict(g)
# 结果：{'student1':93,'student5':98}

# 集合解析
# 场景：产生20个随机数集合，找出能被3整除的集合
s = {randint(0,20) for _ in range(20)}
{x for x in s if x % 3 == 0}
g = filter(lambda x:x % 3 == 0,s)
set(g)

```

#### 2.2 如何为元组中的每个元素命名，提高程序可读性

```
# 固定格式的数据，例如：（名字，年龄，性别，邮箱）
('zhuzhu',23,'male','422914219@qq.com')
('haha',24,'male','zhuonan2018@gmail.com')
# 优势：占用空间小，比字典小
# 缺点：访问时，需要使用索引访问，大量索引降低了程序可读性

# 方案：
1.定义一系列数值常量或枚举
2.使用标准库中collections.namedtuple替代内置tuple

# 数值常量代替数字
Name = 0
AGE = 1
SEX = 2
EMAIL = 3
or 
NAME,AGE,SEX,EMAIL = range(4)

# 枚举
from enum import IntEnum
class StudentEnum(IntEnum):
	NAME = 0
	AGE = 1
	SEX = 2
	EMAIL = 3

s = ('zhuzhu',18,'male','422914219@qq.com')
s[StudentEnum.NAME]
=> 'zhuzhu'
# 判断StudentEnum是不是int的实例
isinstance(StudentEnum.NAME,int)
=> True

# 使用namedtuple (命名元组)
from collections import namedtuple
Student = namedtuple('Student',['name','age','sex','email'])  // 工厂模式，创建一个元组的子类
# 创建命名元组
s2 = Student('Jim',16,'male','ddd@qq.com')
isinstance(s2,tuple)
=> True
s2[0]
=> 'jim'
s2.name
=> 'jim'

```

#### 2.3 如何根据字典中值的大小，对字典中的项排序

```
# 元组的比较
(3,2)>(1,4) 
=> True
(3,2)>(3,4) 
=> False
# 说明：比较两个元组中的第一项，如果有结果那就返回结果，如果相等，则比较第二个数据

# 将字典中的每一项，转换成（值，键）的形式，然后进行大小比较
# 示例代码：产生随机的字典
from random import randint
d = {k:randint(60,100) for k in 'abcdefgh'}
// 通过列表解析的方式构建
l = [(v,k) for k,v in d.items()]  // 为了便于排序，将键和值反过来
sorted(l) // 从低到高排序
sorted(l,reverse=True)  // 从高到低排序
# OR
// 通过zip内置函数实现
l = list(zip(d.values(),d.keys()))

# zip示例
zip([1,2,3],[4,5,6])
=> 返回构造器对象
list(zip([1,2,3],[4,5,6]))
=> [(1,4),(2,5),(3,6)]

# 直接对d.items()返回的元组列表进行排序
# 传递sorted函数的key参数
sorted(d.items(),key=lambda item:item[1],reverse=True)

# 现在再增加排名的次数
p = [('h',99),('g',98),('d',97)]
enumerate(p) // 返回构造器对象
list(enumerate(p))
=> [(0,('h',99)),(1,('g',98)),(2,('d',97))]
list(enumerate(p,1))  // 默认从0开始，可设置起始值
=> [(1,('h',99)),(2,('g',98)),(3,('d',97))]

# 还原为字典
for i,(k,v) in enumerate(p,1)
	d[k] = (i,v)
# OR
{k:(i,v) for i,(k,v) in enumerate(p,1)}

=> {'h':(1,99),'g':(2,98),'d':(3,97)}

```

#### 2.4 如何统计序列中元素的频度

```
# 生成一个重复的序列
from random import randint
data = [randint(0,20) for _ in range(30)]
方案一：
// 根据列表生成包含键的字典，默认值为零，之后用这个字典来记录重复的频数，和排序
d = dict.fromkeys(data,0)
// 迭代列表，统计频数
for v in data:
	d[v] += 1
// 取前三
sorted([(v,k) for k,v in d.items()],reverse=True)[:3]  // 列表解析
sorted(((v,k) for k,v in d.items()),reverse=True)[:3]  // 生成器解析
# 优化：上面的排序方式，对所有的数据进行了排序，性能不好，应该用堆数据
import heapq
heapq.nlargest(3,((v,k) for k,v in d.items()))  // 也是取前三个

方案二：
// 使用标准库collections中的Counter对象
from collections import Counter
c = Counter(data)
c.most_common(3) 

# 词频统计
// 读取文件
txt = open('example.txt').read()
// 单词切割
import re
word_list = re.split('\W+',txt)
c2 = Counter(word_list)
c2.most_common(10)

```

#### 2.5 如何快速找到多个字典中的公共键（key）

```
# 生成多个字典
from random import randint,sample
// sample作用
sample('abcdefgh',3)
=> ['d','e','f']
d1 =[k:randint(0,4) for k in sample('abcdefgh',randint(3,6))]
d2 =[k:randint(0,4) for k in sample('abcdefgh',randint(3,6))]
d3 =[k:randint(0,4) for k in sample('abcdefgh',randint(3,6))]

# 方案1
res = []
for k in d1:
	if k in d2 and k in d3:
		res.append(k)
OR 
[k for k in d1 if k in d2 and k in d3]

# 方案2
dl = [d1,d2,d3]
[for k in dl[0] if all(map(lambda d:k in d,dl[1:]))]

# 利用集合（set）的交集操作
步骤：
1. 使用字典的keys()方法，得到一个字典keys的集合
2. 使用map函数，得到每个字典keys的集合
3. 使用reduce函数，取所有字典的keys集合的交集

// 取交集
{1,2,3}&{3,4,5}
=> {3}
from functools import reduce 
reduce(lambda a,b:a&b,map(dict.keys,dl))  

```

#### 2.6 如何让字典保持有序

```
# 字典是无序的，字典迭代的结果和我们向字典中插入的顺序是没有关系的
# 示例代码：
d = {}
d['b'] = 1
d['c'] = 2
d['a'] = 3
list(iter(d.keys()))
迭代出来的结果可能是和我们插入的顺序是不同的

# 解决方案：
# 使用标准库collections中的OrderedDict，以OrderedDict替代内置字典Dict，迭代顺序和输入顺序一样以外，其他和普通的字典都是一样的
from collections import OrderedDict
od = OrderedDict()
od['c'] = 1
od['b'] = 2
od['a'] = 3
od.keys()
# 结果和插入顺序一致，虽然是有序的，但是不支持切片功能
players = list('abcdefgh')
// 随机打乱顺序
from random import shuffle
shuffle(players)
for i,p in enumerate(players,1):
	od[p] = i

from itertools import islice
# 示例：
list(islice(range(10),2,4))
# 查询名次或范围
def query_by_order(d,a,b=None):
	a -= 1
	if b is None:
		b = a + 1
	return list(islice(od,a,b))

```

#### 2.7 如何实现用户的历史记录功能（最多n条）

```
# 双端队列，两端都能入/出队
from collections import deque
// 第一个参数初始值，第二个参数表示双端队列的长度
q = deque([],5)
q.append(1)
q.append(2)
q.append(3)
q.append(4)
q.append(5)
q.append(6)
结果：超过的自动推出
存在内存中的，如果退出，就会消失
所以需要本地化

# pickle模块，将双端队列处在本地磁盘中
import pickle
// 第一个存储的对象，第二个参数文件对象，文件对象中需要设置读写权限，并且要求是以二进制的形式
pickle.dump(q,open('save.pkl','wb'))
// 读取文件
q2 = pickle.load(open('save.pkl','rb'))

```

#### 猜数字游戏

```
# coding=utf-8
from random import randint
from collections import deque
import pickle


def guess(n, k):
    if n == k:
        print('猜对了，这个数字是%d' % k)
        return True
    if n < k:
        print('猜大了，比%d小' % k)
    elif n > k:
        print('猜小了，比%d大' % k)
    return False


def main():
    try:
        h = deque(pickle.load(open('save.pkl','rb')),5)
    except FileNotFoundError:
        h = deque([],5)
    # h = deque([], 5)
    n = randint(1, 100)
    i = 1
    while True:
        line = input('[%d] 请输入数字：' % i)
        if line.isdigit():
            k = int(line)
            h.append(k)
            i += 1
            if guess(n, k):
                break
        elif line == 'quit':
            pickle.dump(h,open('save.pkl','wb'))
            break
        elif line == 'h?':
            print(list(h))


main()
		
```

