## Python 学习

#### 用途

爬虫，大数据，测试，AI，web，脚本处理等

#### 途径

知乎专栏：小楼昨夜又秋风

#### 主流版本

Python2.x和Python3.x   两个完全独立的版本

#### 基本数据类型

#### Number：数字

```
int 整数
float 浮点数 没有单精度（float），双精度（double）这么一说
判断类型  type()
type(2/2) 浮点型
type(2//2) 整型
单斜杠表示除法带小数点
双斜杠表示整除，只保留整数位
10进制，2进制，8进制，16进制
16进制到9之后，用abcdef来表示

不同进制之间进行转换
0b10 表示2进制 以0b开头的数字表示二进制
0o10 以0o开头的数字表示八进制
0x10 以0x开头的数字表示十六进制
十进制直接书写

type查看值的类型
bin()实现将其他进制的数转换成二进制
int()实现将其他进制的数转换成十进制
hex()实现将其他进制的数转换成十六进制
oct()实现将其他进制的数转换成八进制

bool布尔类型：表示真假 归到数字这个大分类下面
True False  首字母大写
type(True) => bool
int(True) => 1
int(False) => 0
bool(1) => True
bool(2) => True
bool(-1) => True
bool('abc') => True
bool('') => False  // 空字符串
bool([]) => False  // 空列表
bool({}) => False // 空对象
bool(0) => False // 数值，非0表示True 0表示False
bool(None) => False
非空一般都会认为是False 

complex复数
以j结尾表示复数
```

#### 字符串 str

```
如何表示字符串
单引号，双引号，三引号

# 单引号
'abc hello world'
# 双引号
"abc hello world"
# 单引号和双引号用法（内嵌，将引号作为字符串操作）
"let's go !"
# OR
'"hello" is a world'
# OR
'let\'s go' (反斜杠，转义字符)
# 三引号 （建议一行79个字符）通过三引号实现多行字符串表示
'''
hello world 
nihao 
dengdeng
'''
=>'\nhello world\nnihao\ndengdeng\n' (\n=>回车)
# OR
"""
hello world 
nihao 
dengdeng
"""
通过idle在字符串中加入转义字符，并不会有效果
'''hello\nnihao'''
=> 'hello\nnihao'
通过print函数会将转义字符解析
print('hello\nnihao')
=> hello
nihao
当把字符串写在一行，三引号，双引号，单引号意义一样 【记忆】
在idle中也可以通过单引号和双引号实现字符串换行
'hello\
world'
=>'helloworld'
```

#### 转义字符 【特殊的字符】

```
无法’看见‘的字符
例如：\n 换行符
与语言本身语法有冲突的字符
例如：'let\'s go!'
\n 换行 \回车

print 函数中输出转义字符
print('hello \\n world')
# 其他方式
print('c:\\northwind\\northwest')
=> 'c:\northwind\northwest'
pirnt(r'c:\northwind\northwest')
=> 'c:\northwind\northwest'
# 加‘r'之后，字符串不是一个普通字符串，而是原始字符串
# 处理情况不包括
print(r'let's go')  // 通过不了
```

#### 字符串常用操作

```
# 合并字符串
'hello'+'world'
=> 'helloworld'
'hello'*3 [只能*数字]
=> 'hellohellohello'
# 获取字符串中的某个字符
'hello world'[0]
=> 'h' 数组方式，从0开始
'hello world'[-1]
=> 'd' '-1'这种方式，python独有
正数代表序号，负数代表从末尾往前数
示例：获取'hello world'字符串中的‘w'
'hello world'[-5] OR 'hello world'[6]
# 获取某段字符
'hello world'[0:5] 最后截取字符的下一位
'hello'
'hello world'[0:-1] 
'hello worl' 负数不再表示序号，表示的是步长
示例：'hello world' 截取‘world‘
'hello world'[6:11]
'hello world'[6:20]
'hello world'[-5:]  // 从末尾数5个步长，截取到末尾
'hello world'[6:]  // 从下标6开始，截取到末尾
以上这些操作统称为切片
# 原始字符串
r'C:\window'
# OR 大小写都可以
R'C:\window'
```

#### 组（序列）str，list，tuple 

```
# 列表 (list)
[1,2,3,4,5],['hello','world',1,9,True,False],[[1,2],[3,4],[True,False]]
# 以上数据类型 type([1,2,3,4,5]) => list
# [[1,2],[3,4],[True,False]] 这种形式的列表，python中称为嵌套列表
# 访问列表中的元素
[1,2,3][1] => 2 
[1,2,3,4][0:2] => [1,2] 这种形式称之为切片
[1,2,3,4][-1:] => [4]
# 单个选择，返回值；通过冒号方式，返回列表

# 更改列表中的值
# 合并列表
[1,2,3]+[4,5,6] => [1,2,3,4,5,6]
[1,2]*3 => [1,2,1,2,1,2]

# 元组(tuple)
(1,2,3,4,5),(1,'-1',True)
# 内部元素的访问
(1,2,3,4)[0] => 1
(1,2,3)[0:2] => (1,2)
(1,2,3)+(4,5,6) => (1,2,3,4,5,6)
(1,2,3)*3 => (1,2,3,1,2,3,1,2,3)
# ()既可以表示元组，也可以表示为运算，硬性规定：如果()中只有一个元素，编译器视为运算
# 如何定义只有一个元素的元组
type((1,))
# 如何表示一个空的，没有元素的元组
type(())
# 判断序列中是否含有某个元素
3 in [1,2,3,4,5,6] => True
10 in [1,2,3]
# 判断是否不在序列中
3 not in [1,2,3] => False

# 序列共有的特点
# 序列长度
len([1,2,3,4,5,6]) => 6
len('hello world') => 11
# 求最大
max([1,2,3,4,5]) => 5
max('hello world') => 'w' ascll 排列
# 求最小
min([1,2,3]) => 1
min('hello world') => ' '

# 查看ascll
ord('w') => 119
ord('d') => 100
ord(' ') => 32

# 集合（set） 特征：无序，不重复
{1,2,3,4,5,6}
# 不存在的操作
{1,2,3,4,5,6}[0] 不存在 报错
{1,2,3,4,5,6}[0:2] 不存在 报错
# 不重复
{1,2,3,4,3,3} => {1,2,3,4}
# 操作
len({1,2,3}) => 3
1 in {1,2,3} => True
1 not in {1,2,3} => False
# 示例
# {1,2,3,4,5,6} {3,4} 将 第二个集合中的元素，从第一个集合中剔除
{1,2,3,4,5,6}-{3,4} => {1,2,5,6} 操作：求两个集合的差集
# {1,2,3,4,5,6} {3,4} 求交集
{1,2,3,4,5,6}&{3,4} => {3,4}
# {1,2,3,4,5,6} {3,4} 求并集(合集)
{1,2,3,4,5,6}|{3,4} => {1,2,3,4,5,6}
# 定义空的集合
type(set()) => set

# dict 字典  是集合类型（set） 无序
{key1:value1,key2:value2...}
示例：
{1:1,2:2,3:3}
{'sex':'male','age':23}
# 相关操作
{'sex':'male','age':23}['sex'] => 'male'
{'sex':'male','sex':'famale','age':23} => {'sex':'famale','age':23} 字典不能有相同的key
{1:'sex','1':'haha',2:'demo'} => {1: 'sex', '1': 'haha', 2: 'demo'}
{1:'sex','1':'haha',2:'demo'}[1] => 'sex'
{1:'sex','1':'haha',2:'demo'}['1'] => 'haha'
# 空字典表示方式
{}
# 总结：
value: str int float list set dict
key: 必须是不可变的类型 例如：int, str, tuple
{(1,2):'str','dict':{'key':'value'}} => {(1, 2): 'str', 'dict': {'key': 'value'}}

# 基本数据类型总结

数字（Number）包含：整型（int），浮点型（float），布尔型（boolean），复数（complex）
组包含：序列（字符串【str【不可变】】，列表【list】，元组【tuple】）【有序，可用下标来访问，切片操作[0:5]】，集合（set【无序，没有索引，不能切片，不能重复】，字典（dict【key:value 键值对是最基本的概念】））

```

#### 变量与运算符

```
# 变量
# 定义变量
A=[1,2,3,4,5,6]
>>> A=[1,2,3,4,5,6]
>>> print(A)
[1, 2, 3, 4, 5, 6]

扩展知识：小整数对象池：python在执行的时候，为了节约空间，帮我们创建好了小整数对象池，[-5~256]，都是固定的地址，不管你用不用，都会存在。
比如，a=5,b=5,id(a)和id(b)的地址都是小整数池中固定已经存在的地址，所以相等
但如果，a=1000,b=1000,这时会给a一个地址，也会给b一个地址，但他们都不相等。

# 规则：
1. 不能以数字开头
2. 不能是系统保留关键字 
3. 区分大小写的
4. 没有类型限制

# int，str， tuple值类型  list，set，dict引用类型

# 显示某个变量在内存中的地址 id
b = 'hello'
id(b)

# 字符串是不可改变类型，体现
'python'[0] = 'o'
# OR
>>> a='python'
>>> print(a)
python
>>> a[0]='c'
Traceback (most recent call last):
  File "<pyshell#37>", line 1, in <module>
    a[0]='c'
TypeError: 'str' object does not support item assignment
>>> print(a)
python

# list 追加元素
b = [1,2,3]
b.append(4)

# 元组用处
能用元组就用元组，动态的用列表，保持程序的稳定性，有利于多人合作开发
示例：
a = (1,2,3,[1,2,4]) 访问元组中列表中的元素
a[3][2] => 4
a = (1,2,3,[1,2,['a','b','c']]) 访问最内部的数据
a[3][2][1]

```

```
# 运算符

# 算术运算符
1+2 => 3
3-1 => 2
3/2 => 1.5
3//2 => 1 整除
5%2 => 1 求余
2*2 => 4
2**3 => 8 次方
2**5 => 32

# 赋值运算符
=，+=，-=，*=，*=，%=

# 比较运算符
=，>,>=,<=,!=

# 题目：
b+=b>=1
=> 2
原因：b+=b>=1  ==>  b = b + True   ==>  b = b + 1  ==>  b = 2

"a">'b' => False  比较ascll
'abc' < 'abd' => True 字符串中的每个字母单独拿出来比较

# 逻辑运算符
逻辑运算符操作的是bool类型，返回的也是bool类型（是包含转换关系的） 
and 表示且，如果两个都是true，返回true
or 表示或，只要有一个为true，就返回true
not 表示非 not True => False
>>> 1 and 'app'
'app'
int float 0被认为false，非0表示True
str 空字符串将被认为False，其他表示True
list []空的列表被认为False，其他True
tuple set dict和list应用的一样
返回的规律：
返回最终用来判断结果的那个值
not 返回True/False

# 成员运算符
in / not in
1. 判断一个元素，是否在另一组元素内
2. 返回的依然是bool类型
示例：
a = 1
a in [1,2,3,4]
=> True

a = 1
a not in [1,2,3,4]
=> False

str,tuple,list

# 成员运算符（字典）
b = 'a'
b in {'c':1}
=> False
b = 1
b in {'c':1}
=> False
b = 'c'
b in {'c':1}
=> True
判断依据：字典中的key

# 身份运算符
is /is not
返回结果是bool
==比较的是值是否相等，is不是比较值是否相等，is比较的是两个变量的身份是否相等
身份：两个变量的内存地址
a = 1
b = 1.0
a is b
=> False

a = 1
b = 1.0
a == b
=> True

# 说明
>>> a = 1
>>> b = 1
>>> a is b
True
>>> id(a)
4326378288
>>> id(b)
4326378288
>>> 

# 思考题
a = {1,2,3}
b = {2,1,3}
a == b => True 集合是无序的，不会影响取值
a is b => False 内存地址不同

c = (1,2,3)
d = (2,1,3)
c == d => False 元组是有序的
c is d => False 内存地址不同

对象：值，身份和类型。  对象三个特征

判断对象类型
isinstance(a,str) // 判断变量a是否是一个字符串
isinstance(a,(int ,str, float))  // 判断变量a是否是三种类型中的一种

位运算符
& 按位与
| 按位或
^ 按位异或
~ 按位取反
<< 左移动
>> 右移动

关键：把数字当作二进制数进行运算

a = 2
b = 3
a & b
说明：
10 => 2
11 => 3
每一位都进行比较，都为1则为1，否则为0。 则为10

a = 2
b = 3
说明：
10 => 2
11 => 3
每一位都进行比较，只要有一个数为1，则为1，否则为0，则为3

```

#### 表达式

```
# 表达式（Expression）是运算符（operator）和操作数（operand）所构成的序列

示例：
a = 1
b = 2
c = 3
a + b * c => 7
a or b and c => 1 错误答案3 说明：a or b and c  正确运算顺序 a or (b and c) => 1

说明：逻辑运算符 and 优先级高于 or

同级解析顺序：从左至右，左结合

如果有赋值运算符 = 则解析顺序是右结合
示例：
c = a + b
c = a or b => 最后赋值

a = 1
b = 2
c = 2
not a or b + 2 == c => (not a) or ((b + 2) == c) => False

# python 中运算符优先级顺序如下表

# 通过()实现手动改变运算顺序

# 说明： Python 中运算符的运算规则是，优先级高的运算符先执行，优先级低的运算符后执行，同一优先级的运算符按照从左到右的顺序进行。需要注意的是，Python 语言中大部分运算符都是从左向右执行的，只有单目运算符（例如 not 逻辑非运算符）、赋值运算符和三目运算符例外，它们是从右向左执行的。

```

##### 按照优先级从高到底的顺序，罗列出了包括分隔符在内的所有运算符的优先级顺序

| 运算符说明 | Python运算符                         | 优先级 |
| ---------- | ------------------------------------ | ------ |
| 索引运算符 | x[index] 或 x[index:index2[:index3]] | 18、19 |
| 属性访问   | x.attrbute                           | 17     |
| 乘方       | **                                   | 16     |
| 按位取反   | ~                                    | 15     |
| 符号运算符 | +（正号）或 -（负号）                | 14     |
| 乘、除     | *、/、//、%                          | 13     |
| 加、减     | +、-                                 | 12     |
| 位移       | >>、<<                               | 11     |
| 按位与     | &                                    | 10     |
| 按位异或   | ^                                    | 9      |
| 按位或     | \|                                   | 8      |
| 比较运算符 | ==、!=、>、>=、<、<=                 | 7      |
| is 运算符  | is、is not                           | 6      |
| in 运算符  | in、not in                           | 5      |
| 逻辑非     | not                                  | 4      |
| 逻辑与     | and                                  | 3      |
| 逻辑或     | or                                   | 2      |

#### 在文件中编写代码 （idle只是命令行）

```
# 运行python文件顺序
1. 通过idle或者直接通过文本工具建立.py文件
2. 编写代码
3. 保存
4. 通过系统自带的命令行工具，运行‘python xxx.py'来执行
这种通过命令行方式直接运行的py文件，通常称之为入口文件

# 常用开发工具
pycharm(用的很多)，vscode，subline
```

#### python代码解析规范

```
1. python在表达式末尾不强制加分号
2. python不需要{}来包含一组逻辑控制
3. python通过缩进来区分代码和代码块
4. python建议变量用小写；组合单词用下划线表示
5. 对于形式上的常量（因为python中并没有常量的概念，没有相应的检测机制），单词中的所有字母都换成大写
6. python建议每个模块的开头都要有该模块的说明，一段多行注释说明
7. python建议逻辑运算符等，左右两边都留有空格
```

#### python语法

```
# 条件控制
示例：
# demo 1
mood = True
if mood :
	print('go to left')  # print前四个空格 注：确保tab = 4个空格
	print('back awaay')  # 相同缩进判定为代码块
print('demo') # 判定为新的代码块

# demo 2
mood = False
if mood :
	print('go to left')  # print前四个空格
else :
	print('go to right') # 判定为新的代码块
	
# demo 3
# if 判断语句也可以是表达式，总之能转成bool
d = []
if d :
	print('go to left')
else :
	print('go to right')

注：由于python的这种特点，python代码不能被混淆和压缩

# demo 4 
# 接收命令行输入，判断密码是否正确，并给予提示
account = 'nihao'
password = '123456'

user_account = ''
user_pwd = ''

print('please input account')
user_account = input()  # input函数接收命令行输入

print('please input password')
user_pwd = input() 

if account == user_account and password == user_pwd :
	print('login success')
else :
	print('login fail')

# 以上代码存在代码格式报错
# 修改如下

ACCOUNT = 'nihao'
PASSWORD = '123456'

user_account = ''
user_pwd = ''

print('please input account')
user_account = input()  # input函数接收命令行输入

# 变量的定义，建议放在函数，代码块当中；放在最外面模块的变量，都写成常量的形式
print('please input password')
user_pwd = input() 

if ACCOUNT == user_account and PASSWORD == user_pwd :
	print('login success')
else :
	print('login fail')
	
# demo 5
if True
	pass # 空语句/占位语句 在没有想好的时候，用pass来占位
	
# demo 6
if a == 1:
	print('apple')
elif a == 2:
	print('banner')
elif a == 3:
	print('haha')
else:
	print('demo')
	
# demo 7
两个变量，a,b 两个变量其中1个是True1个是False，现在要求返回为True 的变量
方法1:
if else
方法2:
a or b

# 循环控制
while 
# 形式1
while 条件:
	代码块
# 示例：
counter = 1
while counter <= 10:
	counter += 1
	print(counter)
	
# 形式2
while 条件:
	代码块
else:
	代码块
	
# 示例
counter = 1
while counter <= 10:
	counter += 1
	print(counter)
else:
	print('end')
# 当条件为False执行else代码块，之后结束while循环

for 
# 主要用来遍历/循环 序列或者集合、字典
a = ['apple','orange','banner','grape']
for x in a:
	print(x)
	
a = [['apple','orange','banner','grape'],(1,2,3)]
for x in a:
	for y in x:
		print(y,end='') # 修改之后，print打印出的内容都会显示在一行，默认 end='\n'
		print(x,end='|') # 修改之后，print打印出如下 ： 1|2|...这种形式

a = ['apple','orange','banner','grape']
for x in a:
	print(x)
else:
	print('fruit is end')
	
a = [1,2,3]
for x in a:
	if x == 2:
		break # 结束循环 注：如果break存在，使得循环强制结束，不会执行for之后的else代码
	print(x)
for x in a:
	if x == 2:
		continue # 结束当前循环
	print(x)

注：break是结束当前层的循环，如果外层还是有循环，还是会继续执行  js亦是如此

JS：
for(let i = 0 ; i < 10 ; i++){...}
Python:
for x in range(0,10):  # range() 生成指定长度序列 
	print(x)
	
range(0,10) 0表示起始数值，10表示偏移量，
range(0,10,2) 2表示步长 => 0,2,4,6,8
range(10,0,-2) => 10,8,6,4,2

# 思考题
a = [1,2,3,4,5,6,7,8] 打印间隔元素
方法1
for i in range(0,len(a),2):
	print(a[i])
方法2
b = a[0:len(a):2] => 2表示步长，通过切片方式实现
print(b)

# 注释
单行注释 # xxxx
多行注释
'''
xxxxx
'''

# 模块
划分：python是按照文件来划分模块的
```

#### 进阶知识

```
 # Python项目的组织结构
 # 包 => 简单理解，文件夹
 # 模块 => 简单理解，文件 物理表现形式，单独的py文件
 # 类 => 通过类将函数和变量组织起来
 # 函数，变量 属于类本身的特性
 
 理解：用类的方式组织代码，书写在模块（单个文件）中
 
 # 名称，文件夹的名称就作为包的名称，文件的名字就所谓模块的名称
 
 # 区分不同的包的同名模块
 # 包名.模块名 => 命名空间
 # 示例：
 seven.c4
 six.c4
 # 包里可以包含子包，文件夹中含有文件夹
 
 # 文件夹只是包的物理表现形式
 # 如果想把一个文件夹作为包来使用，文件夹内必须有一个‘__init__.py'的文件，
 # __init__.py这个文件可以内容为空，也可以作为一个模块来使用
 # __init__这个模块的名字就是包名
 # 错误的引用方式 seven.__init__
 # 正确的引用方式 seven 通过直接引用包名
 
 # 导入语法
 语法1:
 # 引入模块下的变量
 import module_name
 # 通过命名空间的方式，引入其他包中的模块
 import c7
 print(c7.a)
 也可以通过别名的方式，简化
 import module_name as 别名
 # 这样通过别名.方式就可以简化调用，减少命名空间的书写
 
 # 调用包下的模块，会自动生成_pycache文件夹，
 
 语法2:
 from module import 变量，方法 # 可以用，实现导入多个
 from t.c7 import a
 print(a)
 # 模块中含有多个变量和方法，引入方式
 from t.c7 import *
 print(a)
 print(b)
 print(c)
 
 # 引入包中的模块
 from package import module
 from t import c7
 print(c7.a)
 
 # 在导出的模块内，限制导出
 __all__ = ['a','c'] # 只导出a,c两个变量
 a = 0
 b = 1
 c = 2
 
 # python中一次导入多个模块
 # 换行导入
 # 不推荐方式 
 from c9 import a,b,\
 c
 # 推荐方式
 from c9 import (a,b,
 c)
 
 # __init__.py 文件说明
 # 在导入包的时候，包内的__init__.py文件将会被python自动执行
 # 应用场景
 # 可以决定哪些模块可以被导出
 __all__ = ['c7'] # 只导出c7模块
 
 # 直接引入python中内置的标准库
 # 可以通过在__init__ 模块中导入python中内置的标准库，实现一次导入
 # 示例：
 import t
 # t包内
 print(t.sys.path)
 # 包和模块是不会被重复导入的
 # 不要循环导入，a导入b，b导入a
 
 # python中一旦引入别的模块，就会自动去执行导入模块的代码
 # 注：重复引入的模块只会执行一次
```

#### 函数

```
# python内置函数
round(浮点数,保留位数【四舍五入】)
# 快速查看内置函数
命令行内输入‘python’，打开python命令行
示例：查看某个函数的说明
help(round),回车查看更多

函数特征：
功能性，
隐藏细节
避免编写重复的代码
组织代码

# 函数定义
def funcname(parameter_list):
	pass
	
1. 参数列表可以没有
2. return value 否则python默认返回none

def add(x,y):
	result = x + y
	return result
	
def print(code): # 不要使用python内置的函数名
	print(code)
	
# 注：函数的调用放到函数的定义之后
# python已设置最大的递归调用次数
# python设置最大递归层数
import sys
sys.settrecursionlimit(1000000)  # 设置最大递归层数

# 函数返回多个值
def damage(skill1,skill2):
	damage1 = skill1 * 3
	damage2 = skill2 * 2 + 10
	return damage1 ,damage2

damages = damage(3,5)
print(type(damages)) => <class 'tuple'> # 函数自动包装成元组返回

# 糟糕的提取结果的方式
damages[0],damages[1]....
# 推荐的提取结果的方式
skill1_damage,skill2_damage = damage(3,6)  # 通过新建变量接收返回值 专业名称 序列解包

# 序列解包
a = 1
b = 2 
c = 3
# 简化写成
a,b,c = 1,2,3
# 简写tuple
d = 1,2,3,4,5
type(d) => <class 'tuple'>
a,b,c = d => 序列解包

a = 1
b = 1
c = 1
# 简写
a = b = c = 1

# 参数
1. 必须参数，参数列表中的参数是必须传入的
2. 关键字参数
示例：
def add(x,y):
	result = x + y
	return result
# 通过关键字参数的方式，不需要考虑传参的顺序
c = add(y = 3,x = 2)
3. 默认参数
def add(x=2,y=3):
	result = x + y
	return result
add()
# 默认参数常见问题
1. 必须参数必须在默认参数之前
2. 传参必须按顺序传入，可以通过关键参数实现

```

#### 类

```
# 类的基本定义
# 对属性和方法的封装和描述（基础认识）
class Student():
	age = 0
	name = 'zhuzhu'
	def detail(self):
		print('name:'+self.name);
		print('age:'+str(self.age))

# 实例化
student = Student()
student.detail()

# 不要在一个模块内定义并且实例化类
# 从另一个模块引入类
from 文件 import Student
student = Student()
student.detail()

# 方法和函数的区别
# 方法：设计层面		函数：程序运行，过程式的一种称谓

# 构造函数
class Student():
	age = 0
	name = ''
	def __init__(self,name,age):
		name = name
		age = age
  
  def detail(self):
  	print('name:'+self.name);
		print('age:'+str(self.age))
		
# 实例化
student = Student('zhuzhu',22)
print(student.name) => ''

# 类变量（和类相关联的变量），实例变量（与对象相关联的变量）

class Student():
	# 定义类变量
	sum = 0
	
	def __init__(self,name,age):
		# 定义实例变量
		self.name = name
		self.age = age
		
	def detail(self):
		print(self.name)
		
# 实例化
student = Student('zhuzhu',22)
print(student.name) => 'zhuzhu'
print(Student.sum) => 0

# 变量查找顺序
class Student():
	age = 0
	name = 'haha'
	def __init__(self,name,age):
		name = name
		age = age
  
  def detail(self):
  	print('name:'+self.name);
		print('age:'+str(self.age))
		
# 实例化
student = Student('zhuzhu',22)
# 说明：在实例对象中没有找到该实例变量，回去找类变量，类变量中没有，则会到父类中找
# 注：该行为只有在外部调用的时候才会这样
print(student.name) => 'haha'
print(Student.name) => 'haha'
# 打印字典，实例的属性
print(student.__dict__) => {}
print(Student.__dict__) => {.....}

# self 【并不是必须写成self，python建议写成self】
# 1. 定义实例方法的时候第一个参数必须是self
# 2. 在调用实例方法的时候，不需要对self传参

# 实例方法中调用实例变量和类变量
# 类变量：刻画类的特征
# 方法：代表类的行为
class Student():
	sum1 = 0
	def __init__(self,name,age):
		self.name = name
		self.age = age
		# 访问实例方法
		print(self.name)
		# 访问类方法
		print(Student.sum1)
		print(self.__class__.sum1)
  
  def detail(self):
  	print('name:'+self.name);
		print('age:'+str(self.age))
		
# 类方法
class Student():
	sum = 0
	def __init__(self,name,age):
		self.name = name
		self.age = age
		# 访问类变量
		print(Student.sum)
		print(self.__class__.sum)
	
	# 定义实例方法
	def doAction(self):
		self.__class__.sum += 1
		
	# 定义类方法
	@classmethod (必须显示传入cls)
	def classFunc(cls):
		cls.sum += 1

# 实例化
student = Student('zhuzhu',22)
# 调用实例方法
student.doAction()
# 调用类方法
Student.classFunc()
# 调用类方法（不推荐）
student.classFunc()

# 静态方法
class Student():
	sum = 0
	def __init__(self,name,age):
		self.name = name
		self.age = age
	
	# 定义实例方法
	def introduce(self):
		print(self.name)
		print(self.age)
		print(self.__class__.sum)
		print(Student.sum)
		
	# 定义类方法
	@classmethod
	def classFunc(cls):
		print(cls.sum)
		print(Student.sum)
		
	# 定义静态方法
	@staticmethod
	def staticFunc(x,y):
		print(x + y)
		
# 实例化
student = Student('zhuzhu',22)
# 调用
student.classFunc() => 不推荐
Student.clsssFunc() => 推荐
student.staticFunc(1,2)
Student.staticFunc(2,3)
# 对于静态方法，类似一个存粹的方法，和对象和类没有任何关系
# 注：类方法和静态方法不能访问实例的属性和方法


# 成员的可见性（变量和方法）
class Student():
	sum = 0
	def __init__(self,name,age):
		self.name = name
		self.age = age
		
	# 定义私有方法
	def __privateFunc(self,score):
		# 定义私有变量
		self.__score = score
	
	# 定义实例方法
	def introduce(self):
		print(self.name)
		print(self.age)
		print(self.__class__.sum)
		print(Student.sum)
		
	# 定义类方法
	@classmethod
	def classFunc(cls):
		print(cls.sum)
		print(Student.sum)
		
	# 定义静态方法
	@staticmethod
	def staticFunc(x,y):
		print(x + y)
		
# public 公开的，可在外部访问
# 默认公开，特殊注意：__xxx__的形式也是公开的

# private 私有的，不可在外部访问
# 定义方式：__xxxx 表示方法和属性是私有的，不能直接通过外部访问
# 注：在外部直接访问私有方法，会报错；但是在外部直接访问私有属性不会报错

# 实质：python对成员可见性处理方式非常简单，基本上在外部也是可以访问的
# 在外部直接访问私有变量会报错的原因，例如：__score 属性，python自动转为_Student__score 属性，所以会报找不到该属性；但是，当我们在外部显示调用：student.__score = -1 的时候，所做的操作，实质上是添加了一个名称为 '__score' 的属性，通过 student.__dict__就能看到区别；
# 即使是私有属性也可在外部访问，通过student.__dict__可以看到python自动转换的名称，直接访问该key，即可得到对应的value

# 特性：继承，封装，多态
# 父类
class Human():
	def __init__(self,name,age):
		self.name = name
		self.age = age

	def getName(self):
		return self.name
		
# 子类
from human import Human
# 继承父类
class Student(Human):
	def __init__(self,school,name,age):
		# 调用父类的构造函数
		# 问题：类调用实例方法，这样不好
		Human.__init__(self,name,age)
		self.school = school
		
	def getSchool(self):
		return self.school
		
# 实例化
student = Student('school','zhuzhu',22)
print(student.getName())
print(student.getSchool())
print(student.age)

# 注：实例调用实例方法，不需要传self，python已经自己处理好了
# 但是通过类调用实例方法，需要手动传self
调用方式： Student.getSchool(student)

# 通过super关键字实现继承
# 父类
class Human():
	def __init__(self,name,age):
		self.name = name
		self.age = age
	
	def getName(self):
		print(self.name)
		
	def doHomework(self):
		print('parent class doHomework method')
		
# 子类
from human import Human

class Student(Human):
	def __init__(self,school,name,age):
		# 通过super关键字实现继承，调用父类方法
		super(Student,self).__init__(name,age)
		self.school = school
	
	def doHomework(self):
		super(Student,self).doHomework()
		print('do student homework')
		
# 正常的调用方式
student = Student('school','zhuzhu',18)
```

#### 正则表达式

```
# 基础
c = 'java|python|javascript|c++|c#'
# 基础函数
c.index('python')
'python' in c
# 正则
import re
r = re.findall('python',c)
print(r)
# 规则
if len(r) > 0:
	print(True)
else:
	print(False)
	
# 元字符与普通字符
a = '23l4jljlj43lj4l3j5lj43hk5h6k54'
# 获取所有的数字和非数字
import re
r = re.findall('\d')
r = re.findall('\D')

# 字符集
a = 'abc,acc,afc,adc,aec,agc'
# 找到中间是c或者g的
r = re.findall('a[cg]c',a)
# 找到中间不是c或者g的
r = re.findall('a[^cg]c',a)
# 找到中间是c-f的
r = re.findall('a[c-f]c',a)

# 概括字符集
\d => [0-9]
\D => [^0-9]
\w => [a-zA-Z] 匹配单词字符
\W => [^a-zA-z]
\s => 匹配空白字符，包括空字符串，\n换行 \r会车
. => 匹配除换行符(\n)外的其他字符

# 数量词
t = 'python java1111php'
# 匹配单词
re.findall('[a-z]{3}',t) => ['pyt','hon','jav','php']
re.findall('[a-z]{3,6}',t) => ['python','java','php']  表示数量区间3到6个数字

# 贪婪和非贪婪模式
t = 'python java1111php'
# 默认贪婪模式
re.findall('[a-z]{3,6}',t) => ['python','java','php']  尽可能多的匹配到最长的字符
# 非贪婪模式
re.findall('[a-z]{3,6}?',t) => ['pyt','hon','jav','php']  只要符合最低限度就结束

# 匹配0次1次和无限多次
* => 匹配*前面的字符（紧贴的字符）0次或者无限多次
t = 'pytho0python1pythonn2'
re.findall('python*',a) => ['pytho','python','pythonn']  匹配之前的字符‘n'符合0次或者无限多次，即’pytho‘python’pythonn‘...n无限多个都可以

+ => 匹配+前面的字符（紧贴的字符）1次或者无限多次
t = 'pytho0python1pythonn2'
re.findall('python+',a) => ['python','pythonn']  匹配之前的字符‘n'符合1次或者无限多次，即python’pythonn‘...n无限多个都可以

? => 匹配?前面的字符（紧贴的字符）0次或者1次
t = 'pytho0python1pythonn2'
re.findall('python?',a) => ['pytho','python','python']  匹配之前的字符‘n'符合0次或者1次，即pytho’python  注：最后一个pythonn会截取其中的python 所以很多时候?用来做重复字符串截取

# 注：非贪婪和匹配数量这两个是没有关系的，具体的作用是哪个，主要是看?前是什么，是数量词还是字符

# 边界匹配符
^目标字符串以xxx开始 / $以xxx结束
re.findall('^/d{4,8}$',str) 要求字符串以数字开头以数字结尾，并且长度为4-8位
re.findall('^000',str) 要求字符串以000开头
re.findall('000$',str) 要求字符串以000结尾

# 组
()表示划分一个组
# 示例
t = 'pythonpythonpythonpython'
# 匹配其中的连续三个python
re.findall('(python){3}',t)
# 注意与[]的区别，[]中是或的关系，()中是且的关系

# 匹配模式参数
# 忽略大小写
t = 'java09#C32Python'
r = re.findall('c#',t,re.I)
# 改变.的作用
t = 'java09#C\n32Python'
r = re.findall('c#.{1}',t,re.I) => []
r = re.findall('c#.{1}',t,re.I | re.S) => ['C#\n']   多个匹配模式并存用'|'连接

# re.sub正则替换
# re模块下的正则函数
re.sub(正则，替换字符，目标字符串，匹配数量【默认0，即全部匹配】，匹配模式)
t = 'java23#cjavascript3#c2demo'
# 基础
print(re.sub('#c','zhuzhu',t)) => 'java23zhuzhujavascript3zhuzhu2demo'
# 设定替换数量
print(re.sub('#c','zhuzhu',t,1)) => 'java23zhuzhujavascript3#c2demo'
# 具体操作替换
# re.sub 将函数作为参数
def corvert(value):
	print(value) => 是对这次替换的一种描述，包含替换字符串的内容，具体位置
	matched = value.group();
	return '!!' + matched + '!!'
print(re.sub('#c',corvert,t,1)) => 'java23!!#c!!javascript3#c2demo'

# 函数作为处理参数
# 字符串中数值大于等于6返回9，否则返回0
import re
s = 'A8C3721D86'

def convert(value):
	matched = value.group()
	if int(matched) >= 6:
		return str(9)
	else:
		return str(0)

r = re.sub('\d',convert,s)
print(r)

# search和match函数
search匹配整个字符串，找到第一个满足匹配的结果
match是从首字母开始匹配，找到返回结果，否则返回None
都只返回匹配到的第一个
t = '123ouou543'
print(re.match('\d',t))
re.match('\d',t).span()  // 返回位置信息
print(re.search('\d',t))
re.match('\d',t).group()  // 返回具体内容

# search，match补充说明
t = "A32BD32"
print(re.match('\d',t)) => None 
# 说明：match是从首字母开始匹配，如果首字母匹配则返回结果，否则返回None
print(re.search('\d',t)) => <_sre.SRE_SEARCH ojbect; span=(1,2) ,match="3">
# 匹配整个字符串，找到第一个满足匹配的结果
# 共同点：都是只返回第一个匹配到的结果，匹配之后立即停止

# group分组
# 示例
t = 'life is short , i use python , i love python'
# 匹配life和python之间的字符，匹配两个python之间的字符
result = re.search('life(.*)python(.*)python',t)
# 默认是完整正则匹配
print(result.group(0)) => life is short , i use python , i love python 
# 获取匹配分组结果
print(result.group(1)) => ' is short , i use '
# 获取两个python之间的结果
print(result.group(2)) => ' , i love'
# 集中输出
print(result.groups())
# OR
print(result.group(0,1,2))

# 推荐用findall，直接返回分组的结果
result = re.findall('life(.*)python(.*)python',t) => [' is short , i use ',' , i love']

# 学习建议
1. 分析常用的正则表达式
2. 在不影响工作效率的情况下，多使用正则加深印象
```

#### JSON

```
# 介绍
# 一种轻量级的数据交换格式
# 字符串是json的表现形式
# 符合json格式的字符串就是json字符串
# 易于阅读，解析，网络传输效率高，跨语言交换数据

# 反序列化
# 由字符串转换成语音对应数据结构，叫做反序列化(和是不是json无关，也可以是xml)
# 示例
import json
json_Object = '{"name":"zhuzhu","age":22,"famale":true}'
json_Arr = '[{"name":"zhuzhu","age":22,"famale":true},{"name":"zhuzhu","age":22,"famale":true}]'
dictObject = json.loads(json_Object) => 转换成字典
listArr = json.loads(json_Arr) => 转换成列表

# 对应转换表
json      python
Object    dict
Array 		list
true			True
false 		False
null			None
String 		str
Number		int
Number		float

# 序列化
import json
obj = [{name:'zhuzhu',age:18,male:True},{name:'zhuzhu',age:18,male:True}]
python_json = json.dumps(obj)
print(python_json)

# json对象，json，json字符串
# json对象：对于javascript来说就是js对象，但是对于不同的语言，转换成的数据类型是不同的
# json一种用来传输数据的数据格式
# json字符串，符合json格式的字符串

```

#### 枚举

```
# python 2.x中是没有枚举的
# python 3.x才有
# 枚举本质上就是一个类
# 继承Enum
# 枚举标识最好用大写表示
# 示例：
from enum import Enum

class VIP(Enum):
	YELLOW = 1
	GREEN = 2
	BLACK = 3
	RED = 4

print(VIP.YELLOW)  => 'VIP.YELLOW'

# 枚举类比普通类的优势
# 普通的表示方式：普通变量，字典，类
# 缺陷：1.可变 2.没有防止相同标签的功能

# 枚举类型、枚举名称与枚举值

# 获取对应的数值（枚举值）
print(VIP.YELLOW.value) => 1

# 获取标签的名称（枚举名称）
print(VIP.YELLOW.name) => 'YELLOW'

print(type(VIP.YELLOW.name)) => <class 'str'>
print(type(VIP.YELLOW)) => <enum 'VIP'>
print(VIP['YELLOW']) （枚举类型）

# 枚举是可遍历的
for v in VIP:
	print(v)

# 输出
VIP.YELLOW
VIP.GREEN
VIP.BLACK
VIP.RED

# 枚举的比较运算
from enum import Enum
class VIP(Enum):
	YELLOW = 1
	BLUE = 2

class VIP2(Enum):
	YELLOW = 1
	BLUE = 2
	
# 示例
print(VIP.YELLOW == VIP.YELLOW) => True
print(VIP.YELLOW == VIP.BLUE) => False
print(VIP.YELLOW == VIP2.YELLOW) => False
print(VIP.YELLOW > VIP.BLUE) => 报错,throw error
print(VIP.YELLOW is VIP.YELLOW) => True
# 结论
1. 枚举类型之间可以做等值比较和身份比较，不能做大小比较
2. 比较的是两个枚举类型，不是对应的值

# 枚举注意事项
1. 标签名不能相同，但是值可以相同，相当于别名
2. 遍历的时候不会遍历别名，数值相等第二个不会作为独立的枚举类型
3. 如果需要遍历出别名

   from enum import Enum
      class VIP(Enum):
         YELLOW = 1
         YELLOW_ALIAS = 1
         BLUE = 2
         
	 for v in VIP.__members__.items():
	 		pirnt(v)
	 		
	 # 元组		
	 ('YELLOW',<VIP.YELLOW:1>)
	 ('YELLOW_ALIAS',<VIP.YELLOW:1>)
	 ('BLUE',<VIP.BLUE:2>)

	 for v in VIP.__members__:
	 		pirnt(v)
	 
	 # 输出
	 YELLOW
	 YELLOW_ALIAS
	 BLUE

# 枚举转换
# 数据库中建议通过存取键值的方式来表示类型
# 代码中建议通过枚举类型来区分类型，这样利于代码可读性
# 通过数值转换成对应的枚举类型

from enum import Enum
class VIP(Enum):
	YELLOW = 1
	YELLOW_ALIAS = 1
	BLUE = 2
a = 1
print(VIP(a)) => VIP.YELLOW

# 枚举小结
# 数值限制，必须是int类型
from enum import IntEnum
class VIP(IntEnum):
	YEllOW = 1
	BLUE = 2
# 如果数值为非Int，则会报错

# 数值限制，不能重复，不出现别名的情况
from enum import Enum,unique
@unique
class VIP(Enum):
	YELLOW = 1
	# GREEN = 1 这种情况会报错的
	BLUE = 2
# 数值不能重复

# 枚举类无法实例化，是采用单例模式构建的
# 对于设计模式，建议在写平台的时候多考虑下，如果只是业务上的实现，设计模式不是必须的

```

#### 闭包

```
# 进阶的目的
# 业务逻辑的开发者，不考虑太多的封装性
# 包，类库的开发者

# 一切皆对象 (对比java而言，定义的函数可以赋值给变量)
# 函数式编程
# 闭包

# 什么是闭包
# 闭包 = 函数 + 环境变量（1.在定义函数时的外部 2.相关的变量也不能是全局变量）

# 示例：
def curve_pre():
	a = 25
	def curve(x):
		return a*x*x
	return curve
a = 10
f = curve_pre() // 返回的就是闭包，不止包含函数，还有定义函数时的环境变量
// 输出闭包的环境变量
print(f.__closure__) => (<cell at xxxxx: int object at xxxxx>)
// 环境变量a的值
print(f.__closure__[0].cell_contents) => 25
// 具体的数值
print(f(2)) => 100

# 描述：
由函数以及函数在定义时候的外部环境变量构成的整体，称之为闭包

# 具体示例

# 闭包的意义
# 将函数定义时的现场一起保存

def f1():
	a = 10
	def f2():
		// a此时被python认为是局部变量
		a = 20
		print(a)
	print(a)
	f2()
	print(a)
f1()

# 结果：10 20 10
# 构成闭包必须要做到函数引用外部的环境变量，这样才能算是闭包

# 出个题用闭包解决
# 场景：旅行者累加路程
# 非闭包解决问题
sum = 0;
def go(add):
	new_pos = sum + add
	sum = new_pos;
	return new_pos;
print(go(3))
print(go(5))
print(go(8))

# 以上写法是错误的，在方法内如果存在全局变量名在等号的左边，python会认为定义了一个同名的局部变量，所以上面的方法执行会直接报错，在定义的时候定义了一个局部变量sum，但是执行的时候 new_pos = sum + add 中sum并没有初始化值，所以会报错；
# 作用域链向外查找的前提是，在方法内没有将同名的变量写在等号左边，这样写表示定义局部变量，这样就不会再向外找全局变量
# 通过global关键字申明变量为全局变量，这样就能解决问题

# 改动
sum = 0;
def go(add):
	global sum
	new_pos = sum + add
	sum = new_pos;
	return new_pos;
print(go(3))
print(go(5))
print(go(8))

# 闭包解决
origin = 0
def factory(pos):
	def go(step):
		pos += step
		return pos
	return go
tourist = factory(origin)
print(tourist(3))
print(tourist(5))
print(tourist(8))

# 以上代码运行也会报错，内容和之前一样，都是因为pos被认为是局部变量，并且没有初始化值
# 此时pos的位置即不是全局变量，这无法用global关键字，同时也不应该是局部变量，python提供了nonlocal关键字表示这种变量

# 改动
origin = 0
def factory(pos):
	def go(step):
		nonlocal pos
		pos += step
		return pos
	return go
tourist = factory(origin)
print(tourist(3))
print(tourist(5))
print(tourist(8))

# 虽然通过闭包的方式看着确实比较麻烦，但是通过这种方式实现了函数的封闭性，不会因为全局变量的改变而使得函数功能发生改变，利于代码维护

# 小谈闭包和函数式编程
# 注意：因为闭包不仅保存了函数，还保存了定义时的环境，这些都是常驻内存中的，要防止造成内存泄漏

```

#### 函数式编程

```
# 匿名函数
# 显式声明函数
def add(x,y):
	return x + y
# 匿名函数
lambda parameter_list:expression 
# parameter_list 入参
# expression 表达式，只能是表达式，不能是代码块，也不能是赋值操作

# 示例
lambda x,y:x+y 同上面的add函数一样，不需要return

# 三元表达式
[条件为真时返回的结果] if [条件判断] else [条件为假时的返回结果]

# 示例
x = 1
y = 3
r = x if x > y else y
print(r) => 3

# 常和匿名函数放在一起使用

# map
# map是个类
# 场景：
list_x = [1,2,3,4,5]
def square(x):
	return x*x
r = map(square,list_x)
# map返回的是一个map对象，需要用list方法才能转成列表
print(list(r))

# map和lambda结合使用
r = map(lambda x: x*x,list_x)  

list_x = [1,2,3,4,5]
list_y = [1,2,3,4,5]
r = map(lambda x,y:x*x + y,list_x,list_y)

// lambda表达式参数的个数和map列表数量一样
list_x = [1,2,3,4,5]
list_y = [1,2,3,4,5]
r = map(lambda x,y:x*x + y,list_x,list_y)

list_x = [1,2,3,4,5]
list_y = [1,2,3]
r = map(lambda x,y:x*x + y,list_x,list_y)
// 如果数量不同，则只会输出最小的个数
// 结果 [2,6,12]

# reduce
# reduce没有在全局的命名空间内，需要从functools模块内导出
# reduce是一个函数，累计操作
from functools import reduce

list_x = [1,2,3]
r = reduce(lambda x,y:x + y,list_x)

// 各参数解析
reduce(lambda prev,cur:expression,[list],initValue)

# filter
# 过滤 返回True或False
list_x = [1,0,1,0,1,1,1]
r = filter(lambda x:True if x==1 else False,list_x)
print(list(r))

# 命令式编程VS函数式编程
# 形式上区分：
# 命令式编程 def、for、ifelse
# 函数式编程 map、reduce、lambda


```

#### 装饰器

```
# 用到最多的高阶知识
# java中叫注解
# 开闭原则：对修改是封闭的，对扩展是开放的
# 尽量不要去直接修改函数

# 场景：在每个函数调用的时候打印运行时间

# 不通过装饰器来实现
import time 

def f1():
	pass

def curTime(func):
	print(time.time())
	func()
	
r = curTime(f1)
r()

# 通过以上的方式是可以实现需求，但是这样实现和单独打印时间再调用方法没有什么区别，并没有体现出优势

# 通过装饰器来实现
import time

def f1():
	pass
	
def decorator(func):
	def wrapper():
		print(time.time())
		func()
	return wrapper;
	
# 传统调用方式
# 这种方式并没有体现出装饰器的优势，其实和不采用装饰器来实现没有什么区别，甚至通过嵌套函数的方式来实现，使得更加麻烦
f = decorator(f1)
f()

# 装饰器方式
import time 

def decorator(func):
	def wrapper():
		print(time.time())
		func()
	return wrapper;

@decorator
def f1():
	pass
	
f1();
# 通过装饰器的方式，也就是@语法糖，体现出装饰器的优势，调用方法可以直接通过原函数名进行调用

# 封装原则：可以接受定义时候的复杂，但是不能接受调用时候的复杂

# 带参数的函数，并且参数的个数不是确定的，怎样修改装饰器得以实现呢

# 装饰器方式
import time 

def decorator(func):
	def wrapper(*args):
		print(time.time())
		func(*args)
	return wrapper;

@decorator
def f1(param1,param2,param3):
	pass
	
f1('demo','demo2','demo3');

# 通过不定参数，实现不同个数的传参

# 优化装饰器

# 兼容关键字参数 **kw
import time 

def decorator(func):
	def wrapper(*args,**kw):
		print(time.time())
		func(*args,**kw)
	return wrapper;

@decorator
def f1(param1,param2,param3,**kw):
	pass
	
f1('demo','demo2','demo3',a = 1,b = 2,c = '123');

# 技巧：不定参数，并且不确定是否存在特殊的关键字参数的情况，我们可以通过(*args,**kw) 的方式，包含各种特殊情况的传参

# 装饰器小结
# 利于代码封装，不改变代码内部逻辑，通过装饰器修改功能
# 不破坏封装原则



```

#### 爬虫

```
# 爬虫目的性
# 分析目标数据
# 爬虫：通过对html文件进行文本分析，从而提取需要的数据

```

