# Python 笔记

## 基本语句

Python对大小写敏感  
Python用缩进来表示层次结构

### 数
数是不可变类型
```python
a=(3+4j)			#复数

+ - *
** ：幂。可以是小数、负数
/ ：真除法，结果为实数
// ：Floor除法，结果向下舍入取整
% ：求模
& | ^ ~ << >>：二进制与、或、异或、非、左移、右移
< > <= >= == != :数字比较。可连用
not and or ：布尔逻辑。and：返回从左往右第一个为假的对象。or：返回从左往右第一个为真的对象。（短路运算）
any(序列):一个为真则返回True		all(序列):一个为假则返回False
int(X,N)	#返回N进制X的十进制表示
bin(X), oct(X), hex(X)		#返回X的二进制、八进制、十六进制表示

round(X,N)	#四舍五入，取N位小数
pow(X,N,Z)	#(X**N)%Z
```

### 分支，循环
```python
if 条件:
elif 条件:
else:

正确值 if 条件 else 错误值		#返回值。（短路运算）

字典壹 = {1:"Hello",2:"World"};	answer=字典壹.get(what,"wrong")
#可以替代
if what==1:	answer="Hello"
elif what==2:	answer="World"
else:	answer="wrong"
```
```python
for 元素 in 可迭代对象 :		#从头到尾遍历整个可迭代对象中的元素
else:							#若for中break，则不执行else

while 条件:
else:
```
```python
break
continue
```

### 函数
```python
全局变量
def 函数壹(形参):					#参数传递都是引用。但对于不可变类型其实等价于传值
	global 全局变量					#命名空间声明，避免在为全局变量赋值时生成新的同名局部变量
	函数壹的局域变量
	def 函数贰():
		global 全局变量
		nonlocal 函数壹的局域变量
	return 3
```
```python
def 函数贰( 形参壹, 形参贰=50, *列表甲, 形参叁, **字典乙):			#默认参数后的所有参数都必须有默认值
函数贰( 1,2, 11,12,13, 形参叁=3, name="hi",score=100)				#在星号后的形参叁必须显式关键参数赋值
#默认参数被保存为一个对象，附加在函数本身中。若默认参数为可变对象，则可能被改变，使得第二次调用函数时出现陷阱。

def 函数叁 (a,b,c,d,e)
函数叁( *(0,1), c=2, **{"d":3,"e":4} )			#解包参数。*后可加任何可迭代对象。用于 函数变量=不同函数 的情况
```
```python
函数肆.函数属性甲 = 1			#可向函数附加任意属性。起静态变量作用
```
```python
函数伍 = lambda 形参壹, 形参贰=形参贰默认参数: 返回值表达式				#匿名函数，用于只给一个函数变量赋值
函数伍( 实参壹, 实参贰)
#if要用： 正确值 if 条件 else 错误值
#循环要用：map或列表解析式
#print()要用：sys.stdout.write()
```
```python
def 函数陆( 形参壹:形参壹类型, 形参贰:形参贰类型=50 ) -> 函数返回类型:		# Type Hints
	# 函数陆.__annotations__ 可查看
```

### 引用
变量——引用——>对象
对不可变类型对象，改变变量值为将变量引用的对象改为另一个新对象。
对可变类型对象，改变变量值为将变量引用的对象值改变。
```python
对象贰 = 对象壹		#这是引用
对象壹 is 对象贰		#测试对象身份。判定标准为 id(对象) 是否相同，即是否引用同一块内存
```
函数的参数是引用，若是可变的默认参数，不会每次都创建
```python
def func(a=[]):		#每次调用时默认参数a是上次调用结束时的值
def func(a=None):	a = a or []	#每次调用时默认参数a都是[]
def func(a=None):	if a is None: a=[]	#每次调用时默认参数a都是[]
```

```python
import copy
新对象 = copy.copy(旧对象)				#浅拷贝，仅复制第一层，更深层为引用
新对象 = copy.deepcopy(旧对象)			#深拷贝，递归复制多层嵌套数据结构。#注意：旧对象中若对同一内存引用了多次，则新对象也复制一新内存，对其引用多次。
```


### 其他
行末尾用 \ 将下一行连接起来
```python
# 注释
"""三重引号引字符串块，
   可以拿来做多行注释"""
```

```python
pass	#空语句
vars()		locals()		#返回当前作用域中定义的所有名的集合
dir(对象)		#返回对象实例的属性名称列表。默认参数为当前模块。
对象.__dict__	#返回对象的属性字典。不显示继承属性。
help(对象)		#即 对象.__doc__，对象的文档字符串，对象最顶端的"""字符串，一般用于说明。
id(对象)		#返回对象唯一id（可间接理解为内存地址）
del(变量)		#删除变量
type(对象)						#返回对象类型
isinstance(某变量,某类)			#判断某变量是不是某类
issubclass(某类壹,某类贰)		#判断某类壹是不是某类贰的子类
```

```python
exec( 字符串, 全局作用域, 局部作用域 )
	#执行字符串中的代码。
	 例：exec("print("Hi")")
eval( 字符串, 全局作用域, 局部作用域 )
	#返回字符串中的表达式。
	 例：a=eval("2*3")
	#全局作用域：字典，含内置函数__builtins__
	 局部作用域：字典
	 这两个作用域默认为当前作用域。
	 只在作用域中赋值各变量，也只能读取作用域变量
compile( 字符串,文件名,模式)
	#将字符串编译为字节代码返回，用于被exec或eval快速执行。
	 例：c=compile("print(3)","","exec"); exec(c)
	#模式："single"单个语句，"exec"多个语句，"eval"一个表达式
```



## 序列

```python
序列壹[1]
序列壹[-1]			#负数代表从后往前数，-1是最后一项
序列壹[2][0]
序列壹[起点:终点+1:步长]				#返回从起点到终点的序列。步长负数则倒序，此时需起点>终点。
range(起点, 终点+1, 步长)		#生成一个从起点到终点的数字迭代器，起点默认参数为0，步长默认参数为1
某元素 in 序列壹				#返回bool
a,b = 1,2 	#两个值的序列表达式
a,*b, c = 0,10,11,12,20	#一个值加任意个值的序列表达式
len(序列壹)		#返回序列长度
序列壹+序列贰			序列壹*整数			#合并
sum(数字序列)
新序列 = [ 对元素处理 for 元素 in 原序列 if 条件]			#解析，可生成列表、集合、字典、生成器。可加任意数目for，每个for可选带一个if。例：new_dict = { m:m*2 for m in "asdf" }
```

### 元组
元组为不可变类型
```python
元组壹 = ("wow", 2, ["Hello", "World"], 4+3)		#元组中嵌套的列表可改变
元组壹.index(某值,第几个)		#返回某值编号
元组壹.count(某值)		#返回某值出现次数
```

### 列表
```python
列表壹 = ["wow", 2, ["Hello", "World"], 4+3]		#列表从0开始编号
列表贰 = 列表壹		#列表贰是列表壹的引用。[1,列表壹,2]也是引用
列表贰 = 列表壹[:]	列表贰 = 列表壹.copy()		#列表贰是列表壹的复制
列表壹[i:j] = 任意多个列表值		#可变相实现修改、增加、删除
列表壹.append("yeah")		#在列表末尾增加数据项
列表壹.extend([89, "yep"])		#在列表末尾增加数据项集合
列表壹.remove("Hello")		#删除列表中特定项
列表壹.pop(位置)		#删除指定位置的数据项，并返回该数据项。默认参数为末尾
列表壹.insert(位置, "World")		#在指定位置前面增加一个数据项
del(列表壹[0])		del(列表壹[i:j])	#删除数据项
列表壹.sort()		#升序，替换原数据。参数reverse=True则变为降序。参数key=对每项数据处理得真正排序值的函数
新列表 = sorted(列表壹)	#升序，不替换原数据。参数reverse=True则变为降序。参数key=对每项数据处理得真正排序值的函数
列表壹.reversed()		#颠倒列表，替换原数据。
新列表 = list(reversed(列表壹))		#颠倒列表，不替换原数据。
列表壹.index(某值)		#返回某值编号
列表壹.count(某值)		#返回某值出现次数
```

### 字符
字符串为不可变类型
```python
"%d eggs and %s %s" %(123,45,"vegitables")			等价于			"123 eggs and 45 vegitables"
"123 %(hello)d" %{"hello":"eggs"}		等价于		"123 eggs"
"字符串零{0}字符串壹{1[100].子函数()}字符串贰{形参丙}".format(实参甲, 实参乙, 形参丙=实参丙)		等价于		"字符串零"+str(实参甲)+"字符串壹"+str(实参乙[100].子函数())+"字符串贰"+str(实参丙)
f"字符串零{变量甲}字符串壹"		等价于		"字符串零"+str(变量甲)+"字符串壹"
字符串 = """三重引号引字符串块，
	可以拿来做多行注释"""
字符串=r"\t字符串\n"				#Raw字符串，可无视转义字符，当成普通\n
元组 = 字符串.split(分隔符,段数-1)		#返回元组，内容是将行按分隔符分隔成几个片段。第一个参数默认空白符，第二个参数默认正无穷
索引值 = 字符串.find(查找值)			#返回该行中第一个查找值所在位置索引值，若不存在则返回-1
字符串.strip()	字符串.rstrip()		#返回从字符串中去除最前后空白符后的字符串。不改变原字符串
"分隔符字符串".join(字符串列表)		#在列表的各字符串中插入分隔符，形成大字符串。分隔符为空等价于合并多个字符串，速度快，省内存。
字符串.lower()		字符串.upper()				#返回将所有字符小/大写的字符串。不改变原字符串
字符串.replace(原字符串片段,新字符串片段)		#返回替换的字符串。不改变原字符串
字符串.isdigit()		字符串.isalpha()		#判断字符串是否全数字、全字母
```

#### 编码
Python3的字符串都是Unicode的
Python源文件默认以UTF-8编码
脚本第一行写 # -*- coding: 二进制编码方案 -*- 	告诉编译器本脚本以何种编码方案存储
sys.getdefaultencoding()	#查询系统默认编码
ASCII码：	ord("s")输出115		chr(115)输出"s"
```python
二进制字符串=b"字符串"		#字符串格式化对其无效
二进制字符串 = Unicode字符串.encode("二进制编码方案")
Unicode字符串 = 二进制字符串.decode("二进制编码方案")		#相互转换
Unicode字符串 = str( 二进制字符串, "二进制编码方案" )		#相互转换，返回另一种类型字符串
二进制编码方案：utf-8、unicode-escape、gbk
bytearray字符串 = bytearray(原字符串,"Unicode字符串编码方式")			#将Unicode字符串或byte字符串转为bytearray字符串，若原为byte字符串则无需第二个参数
```

#### 与值间转换
```python
int(字符串)		float(字符串)		#返回字符串中的数。例：int("3")
str(对象)			#返回该对象的字符串表示，对用户更友好
repr(对象)			#返回该对象的字符串表示，全部细节，机器可读（大部分情况下该输出可作为有效python代码）
```

### 集合
集合set为可变类型，frozenset为不可变类型。
```python
集合壹 = {1, "hi"}							#集合中不含重复元素
集合壹 = set(序列壹)
某元素 in 集合壹			#给出bool值
集合壹.add(增加元素)		集合壹.update(增加集合)		集合壹.remove(删除元素)
子集叁<集合壹	集合壹>子集叁	子集叁.issubset(集合壹)	集合壹.issuperset(子集叁)
并集∪：|		交集∩：&		差集：-			(A∪B)-(A∩B)：^
```

### 字典
字典是可变类型
```python
字典壹 = {"KEY_ONE":"Hi", 2:["yeah",34], 3:{5:6, 7:"hello"}}
字典壹["KEY_THREE"] = 233					#修改或添加新项
字典壹.update(字典贰)			#将字典贰加入字典壹中，相同的键则将字典壹覆盖掉
字典壹 = dict.fromkeys(键列表,全部键相同的起始值)
字典壹 = dict( 由每一对键值组成的元祖 的列表 )
字典壹 = dict( 键壹=值壹, …… )
del 字典壹[某键]
字典壹.pop(某键)				#删除某一项，返回某值
字典壹.items()		#返回一个元组的视图对象，每个元组是一对键与值。例：for 键元素,值元素 in 字典壹.items():
字典壹.keys()	字典壹.values()	#返回键、值的视图对象
某键 in 字典壹		#返回bool
for 键 in 字典壹:
字典壹.get(某键,错误值)	#若某键在字典壹中，则返回 字典壹[某键]，否则返回错误值
```

### 迭代器
惰性求值：使用时才产生结果，而非立刻产生结果
```python
迭代器壹 = iter(可迭代对象壹)
next(迭代器壹)				迭代器壹.next()
#以下六个生成的迭代器只能是单个迭代器（有且只有一个指针，每个元素指过即丢）
map( 函数, 可迭代对象壹, ……, 可迭代对象N)		#生成迭代器，等价于：iter( 函数(可迭代对象壹[0],……,可迭代对象N[0]), 函数(可迭代对象壹[1],……,可迭代对象N[1]), ……) 即 iter( 函数(每元素) for 每元素 in zip(可迭代对象壹,……,可迭代对象N) )
zip( 可迭代对象壹, ……, 可迭代对象N)		#并行步进，生成元素为N元元组的迭代器，第i个元组为(可迭代对象壹[i],……,可迭代对象N[i])
enumerate(可迭代对象壹, start=开始序号)			#偏移值，生成元素为二元元组的迭代器，第i个元组为(i,可迭代对象壹[i])
filter( 函数, 可迭代对象)		#生成迭代器，等价于：iter( 每元素 for 每元素 in 可迭代对象 if 函数(每元素)==True )
( 对每元素处理 for 每元素 in 原可迭代对象 if 条件 )		#生成器表达式
#其他内置类型支持多个迭代器
```
```python
import functools
functools.reduce(函数op,可迭代对象,初始值)		#等价于：((((((初始值 op 0) op 1)op 2)op 3)op 4)……)。初始值默认为空
偏函数 = functools.partial(原函数,实参壹,实参贰,形参肆=实参肆)		#调用 偏函数(实参叁) 等价于 原函数(实参壹,实参贰,实参叁,形参肆=实参肆)
```
```python
import operator
operator.add				# +
operator.itemgetter(i)		# [i]
```

### yield
```python
def 生成器函数():
	yield 1
	x = yield 2
	for i in range(5):	yield i+1
G=生成器函数()			#并未开始执行
G.send(10) 或 next(G)	#传入值给上一次调用终点，继续执行函数，至遇到yield为止，返回值，记录下此次终点位置。
						#第一次“调用终点”为程序开始开始
						#next(G) 等价于 G.send(None)
						#若遇到程序终止（自然结束或return），则报错StopIteration
for i in G:				#每次迭代时运行函数
G.close()				#手动关闭
```

```python
def 中间函数():
	yield from 可迭代对象	#当中间函数执行到此行时，等价于中间函数消失，可迭代对象直接面对调用中间函数的代码，直至可迭代对象完毕，才继续中间函数后继代码
```

### 辅助库
```python
import collections
collections.Counter(列表)		#统计列表中各项有多少个重复值
```
```python
import itertools						#单个迭代器，只能用一次
itertools.islice(可迭代对象, 起点,终点+1,步长)
	# 返回迭代器，同切片：可迭代对象[起点:终点+1:步长]。不可为负数
itertools.count(起始值,步长=1)
	# 返回迭代器，生成从起始值开始的连续整数。起始值默认0，步长默认1。
	# itertools.count(2,3):  2,5,8,……
itertools.cycle(可迭代对象)
	# 返回迭代器，反复循环可迭代对象
	# itertools.cycle([2,3]):  2,3,2,3,2,3,……
itertools.repeat(元素,数目)
	# 返回迭代器，重复元素若干次。数目默认无穷。
	# itertools.repeat(2,3):	2,2,2
itertools.tee(可迭代对象,数目)
	# 返回一个指定数目的元组，每个元素是一个迭代器，指向原可迭代对象的复制体。数目默认2
	# itertools.tee([1,2],3):  (1,2),(1,2),(1,2)
itertools.chain(可迭代对象壹,可迭代对象贰,……)
	# 返回迭代器，将所有可迭代序列并成一大序列
	# itertools.chain( (1,2), (3,4,5) ):  1,2,3,4,5
	# itertools.chain.from_iterable( ((1,2),(3,4,5)) ):  1,2,3,4,5
itertools.product(可迭代对象壹,可迭代对象贰,……,repeat=重复数目)
	# 返回迭代器，每个元素是各可迭代对象元素的直积组合。重复数目意为可迭代对象在参数列表中重复。
	# itertools.product([1,2],[3,4],repeat=2):  相当于itertools.product([1,2],[3,4],[1,2],[3,4]):  (1,3,1,3), (1,3,1,4), (1,3,2,3), (1,3,2,4), (1,4,1,3), (1,4,1,4), (1,4,2,3), (1,4,2,4), (2,3,1,3), (2,3,1,4), (2,3,2,3), (2,3,2,4), (2,4,1,3), (2,4,1,4), (2,4,2,3), (2,4,2,4)
itertools.permutations(可迭代对象,数目)
	# 返回迭代器，列表中选出指定数目元素的全排列
	# itertools.permutations((1,2,3),2):  (1,2), (2,3), (2,1), (2,3), (3,1), (3,2)
itertools.combinations(可迭代对象,数目)
	# 返回迭代器，列表中选出指定数目元素的全组合
	# itertools.combinations((1,2,3,4),3):  (1,2,3), (1,2,4), (1,3,4), (2,3,4)
itertools.combinations_with_replacement(可迭代对象,数目)
	# 返回迭代器，列表中选出指定数目元素的全组合（有放回抽取）
	# itertools.combinations_with_replacement((1,2,3),2):  (1,1), (1,2), (1,3), (2,2), (2,3), (3,3)
itertools.compress(可迭代对象壹,可迭代对象贰)
	# 返回迭代器，第i项元素是 可迭代对象壹[i] if 可迭代对象贰[i]
	# itertools.compress( (1,2,3,4,5), (True,False,True) ):  1,3
itertools.takewhile(判断函数,可迭代对象)
	# 保留可迭代对象中开头的所有 判断函数(元素)==True 的元素，丢弃从第一个 判断函数(元素甲)==False 的元素甲开始的所有序列
	# itertools.takewhile( lambda x:x<5, [1,4,6,4,1] ):  1,4
itertools.dropwhile(判断函数,可迭代对象)
	# 丢弃可迭代对象中开头的所有 判断函数(元素)==True 的元素，返回从第一个 判断函数(元素甲)==False 的元素甲开始的所有序列
	# itertools.dropwhile( lambda x:x<5, [1,4,6,4,1] ):  6,4,1
itertools.dropwhile(判断函数,可迭代对象)
	# 返回所有判断为假的元素序列
	# filterfalse(lambda x:x<5,[1,4,6,4,1]):  6
itertools.groupby(可迭代对象,函数)
	# 将函数作用在可迭代对象每一个元素上，将相邻的相同函数值的当作一组。返回迭代器，每个元素是一个二元元组，第一项为该组函数值，第二项为指向原可迭代对象的该组元素的指针。
	# itertools.groupby( (2,4,5,7,9,6), lambda x:x%2 ):  (0,(2,4)), (1,(5,7,9)), (0,(6,))
itertools.starmap(函数,可迭代对象)
	# 类似map。可迭代对象的每个元素是一个 可迭代对象i，*(可迭代对象i) 作为函数的参数。返回迭代器，每一项是函数返回值
	# itertools.starmap( lambda x:x+1, ((3,),(4,),(5,)) ):  4,5,6
itertools.zip_longest(可迭代对象壹,可迭代对象贰,……,fillvalue=默认填充值)
	# 类似zip。长度为最长的可迭代对象长度，不够的填充默认值。"默认填充值"默认None
	# itertools.zip_longest( (1,2,3), (4,5), fillvalue=10 ):  (1,4), (2,5), (3,10)
itertools.accumulate(可迭代对象,函数)
	# 返回迭代器，第i项为对前i项可迭代对象进行函数运算。函数默认为加法
	# itertools.accumulate((2,3,4),operator.mul):  2,6,24
```



## 类

### 类
```python
class 类名 (基类名甲,基类名乙):				#定义类。
	def __init__( self, 参数壹, 参数贰=2 ):		#构造函数
		基类名.__init__( self, 基类所需实参 )  或  super(类名,self).__init__(基类所需实参)		#super(类名,self) 指代基类对象
		self.实例数据成员壹 = 参数壹
	def __del__(self):							#析构函数
	类数据成员贰 = 0
	def 成员函数壹( self, 参数叁):				#成员函数
		类名.类数据成员贰 += 1
		基类名.基类成员函数叁( self, 基类成员函数参数 )
	def 类成员函数贰( 参数肆):
实例化壹 = 类名( 实参壹, 实参贰 )
实例化壹.成员函数壹( 实参叁 )		等价于		类名.成员函数壹( 实例化壹, 实参叁 )
实例化壹.基类成员函数叁( 基类成员函数实参 )
类名.类属性甲 = 1;	实例化壹.实例属性乙=2		#赋新值或新建属性
```
执行class语句（不是调用类）时，会执行类中顶层语句

函数名、类数据成员、类名.属性 是类的属性，self.实例数据成员、实例化壹.属性 是实例的属性。实例会继承类的属性

将属性命名为 __属性名 ，会自动被重命名为 _类名__属性名，
	可用于多继承避免冲突，
	可用于将 __属性名 变相变为私有属性（因真正名字已变为 _类名__属性名）。
搜索数据成员或成员函数时，在继承树中从派生类到基类、从左到右搜索，因此派生类可覆盖掉基类成员

查看类：实例化壹.__class__			查看基类：派生类.__base__	

在类中加入 __slots__=["属性名壹","属性名贰"] 可将类的属性限制在这个列表中，消除类的__dict__，非列表中的属性名不得出现。当基类对属性壹赋值后，派生类属性壹变成只读。__dict__不会显示继承，__slots__会显示继承。


### 操作符重载
```python
__add__、__radd__、__iadd__：+
	__add__(self,other) 是 self+other
	__radd__(self,other) 是 other+self。
	__iadd__(self,other) 是 self+=other
__getattr__(self,属性名)、__getattribute__(self,属性名)、__setattr__(self,属性名,值)、__delattr__： 实例化.属性名
	__getattr__只有在对未定义属性时才有用。
	__getattribute__、__setattr__、__delattr__函数中要避免无限递归不能用 self.属性名 来调用。__getattr__函数中必须用object.__getattribute__(self,属性名)；__setattr__函数中必须用 self.__dict__[属性名]=值 或 object.__setattr__(self,属性名,值)；__delattr__函数中必须用 del self.__dict__["属性名"] 或 object.__delattr__(self,属性名)。
	getattr(实例化,属性名)等同于 实例化.属性名，	setattr(实例化,属性名,值)等同于 实例化.属性名=值
__len__(self)：容器长度
__iter__、__next__：迭代器。可自行用代码实现是单个迭代器还是多个迭代器
__getitem__(self,索引)、__setitem__(self,索引,值)、__delitem__(self,索引)：索引与分片。
	起点:终点-1:步长 本质是 slice(起点,终点-1,步长)
__contains__(self,元素)：元素 in self
__reversed__(self)：self.reversed()
__str__、__repr__：打印。
	直接return要打印的字符串。
	print优先用__str__，交互模式只用__repr__。
	可以先定义好__str__后，直接__repr__=__str__
__call__：函数调用		mul：在原处修改该实例对象
__lt__、__gt__、__eq__、__ne__：比较
__bool__：布尔 		__and__、__or__：&、|
__instancecheck__(self,instance)	__subclasscheck__(self,subclass)
```

### 属性函数
```python
class 类名:
	属性甲 = property(get函数,set函数,del函数,docs函数)
调用 实例化.属性甲 的时候，会根据情况调用 get函数 等
```
```python
class 类名:
	@property
	def 属性甲(self):
	@属性甲.setter
	def 属性甲(self,value):
	@属性甲.deleter
	def 属性甲(self):
调用 实例化.属性甲 的时候，会根据情况调用这三个函数
```
```python
class 描述符甲:
	def __get__(self,instance,owner):		#self是属性，instance是属性的原实例(类的属性的原实例为None)，owner是描述符实例要附加到的类
	def __set__(self,instance,value):		#value是赋予的值
	def __delete__(self,instance):
class 类名:
	属性壹 = 描述符甲()
实例化壹 = 类名()							#则在调用 实例化壹.属性壹 时，相当于调用 描述符甲.__get__(属性壹,实例化壹,类名)
```

### 闭包
等同于含成员变量的类实例化对象
```python
def maker(N):
	def action(X):
		return X**N
	return action
def maker(N):
	action = (lamda X: X**N)
	return action
f=maker(2);  	             	g=maker(3)
f(4);        	f(5);        	g(6)
#输出16即4**2	#输出25即5**2	#输出216即6**3
```

### 装饰器
```python
@装饰器甲
def 函数壹(函数壹的形参):
	函数壹的定义
函数壹(函数壹的实参)
# 等价于
def 函数壹旧(函数壹旧的形参):
	函数壹旧的定义
函数壹新 = 装饰器甲(函数壹旧)   #即调用 装饰器甲(函数壹旧) 或 装饰器甲.__init__(函数壹新,函数壹旧)
函数壹新(函数壹新的实参)    #即调用 函数壹新(函数壹新的实参) 或 装饰器甲.(函数壹新,函数壹新的实参)
```
```python
@装饰器甲
class 类贰:
	类贰的定义
实例化 = 类贰()
# 等价于
class 类贰旧:
	类贰旧的定义
类贰新 = 装饰器甲(类贰旧)
实例化 = 类贰新()
```

```python
# 可自定义
def 装饰器甲(函数旧或类旧):
	return 函数新或类新
# 或自定义
class 装饰器甲:
	def __init__(self,函数旧或类旧):
	def __call__(self,函数新或类新作为函数的形参):
```
```python
def 装饰器包装(装饰器包装形参):
	def 真装饰器(函数旧或类旧):
		用到装饰器包装参数的真装饰器定义
	return 真装饰器
@装饰器包装(装饰器包装实参)
def 函数壹(函数壹的形参):
	函数壹的定义
函数壹(函数壹的实参)
# 等价于
def 函数壹旧(函数壹旧的形参):
	函数壹旧的定义
函数壹新 = 用到装饰器包装参数定义的真装饰器(函数壹旧)
函数壹新(函数壹新的实参)
#类同理
```

### 元类
```python
class 类名(基类名,metaclass=元类名):
	属性壹=值壹
	def 属性贰():
# 实质为
class = 元类名( 类名, (基类名,), {"属性壹":值壹,"属性贰":属性贰,"__module__":"命名空间"})
# 实质为
元类名.__new__( 元类名, 类名, (基类名,), {属性列表})
元类名.__init__( class, 类名, (基类名,), {属性列表})
```

### 环境管理器

```python
class 环境管理器:
	def __enter__(self,形参):
		return 赋给as后的对象
	def __exit__(self,type,value,traceback):
		若本函数return False，则向上一层处理器引发异常

with 环境管理器壹 as 对象壹, 环境管理器贰 as 对象贰:
	with语句块
	#若with语句块引发异常，则调用 __exit__(self,type,value,traceback)
	#若顺利执行with语句块，则调用__exit__(self,None,None,None)
```



## IO

```python
print(要写内容, sep=分隔符, end=结尾字符串, file=文件壹)		#除要写内容外，其他都有默认值
#标准输出即屏幕为：sys.stdout

字符串 = input("输入提示")		#读取用户键盘输入
```


### 文件读写
```python
文件壹 = open("文件名","r",encoding="二进制编码方案")	#打开文件。字符串编码方式可不填
#文件打开方式有：读"r"，清空写"w"，不清空读写"w+"，不清空写"a"，二进制"rb""wb"。
文件壹.close()			#关闭文件
字符串 = 读文件壹.read(N)			#读入从目前指针处开始N个字节。默认参数是到文件末尾
字符串 = 读文件壹.readline()		#从文件中获取一个数据行
字符串列表 = 读文件壹.readlines()		#读取整个文件
写文件贰.write(字符串)	#写入文件
写文件贰.writelines(字符串列表)		#将列表内所有字符串写入文件
for 行 in 文件壹	#遍历文件中每一行
文件壹.seek(N)			#移动到文件偏移量N的位置
文件壹.tell()
```
```python
with open("文件名") as 文件壹：
	处理文件壹
#等价于
try:
	文件壹 = open("文件名")
	处理文件壹
finally:
	if "文件壹" in locates():
		文件壹.close()
```

内存中的伪文件读写。类似C++中的stringstream。可对伪文件调用各种文件接口
```python
from io import StringIO		#字符串形式
from io import BytesIO		#二进制形式
伪文件贰 = StringIO("文件初始内容")		#打开一个伪文件
```



## 异常

```python
try:
	正常运行代码
except IOError:
	出错时的执行代码
except (TypeError,NameError):           #无论出现哪种都捕捉
except ValueError as 异常对象实例化:    #比如：print("Error"+异常实例化.字符串)
except:                                 #其他所有上文未提到异常都走这边
else:                                   #无异常时执行
finally:                                #不论正确与否都会执行

sys.exc_info() #返回元组(异常类型,异常实例,异常最初发生时的堆栈traceback对象)。一般用于最终except无法as对象时
```

```python
class 自定义错误类型( 原有错误类):
     def __init__( self, 形参):
          原有错误类.__init__( self, 实参)
          新增代码
```

```python
raise 异常类 或 异常实例			#手动抛出异常。抛出异常类相当于抛出无构造函数参数的实例
raise 异常实例(构造函数参数)		#构造函数参数存于实例的args元组属性中，print(实例)时自动显示
raise   							#重新引发当前异常，传递给更高层处理器。
raise 新异常 from 已有异常
#异常一旦被except抓住则不会再往高层传，除非手动raise，但若无excep只有finally则会继续往上传
```
```python
assert 条件,错误值               #若条件不满足，则抛出AssertionError(错误值) 。可没有错误值
#稳定版本在编译时用 python -O main.py 来消除arrest并优化程序。__debug__在-O时为False
```
```python
import warnings
warnings.warn(警告字符串)
```
### 预设错误类

| 错误类 | 说明 |
| - | - |
| BaseException | 顶级根类 |
| Exception | 除系统退出外所有根类 |
| ArithmeticError | 所有数值错误 |
| ValueError | |
| ZeroDivisionError | 除数为零 |
| IOError | 输入输出错误 |
| EOFError | |
| TypeError | |
| NameError | 访问未声明变量 |
| StopIteration | |
| KeyboardInterrupt | |
| IndexError | 访问超出序列范围 |
| KeyError | 访问字典中不存在的键 |
| AttributeError | 访问对象中不存在的属性 |
| AssertionError | |
| SyntaxError | 语法错误 |
| pickle.PickleErroe | |



## 大型工程


### import
```python
import 模块壹;								模块壹.变量壹=5
from 模块贰 import 模块贰的变量贰;			变量贰=5						#尽量不要用from，容易出现奇怪陷阱
import 目录壹.子目录贰.模块叁;				目录壹.子目录贰.模块叁.变量肆=5
from 目录壹.子目录贰 import 模块叁;			模块叁.变量肆=5
from . import 模块伍;		from .模块陆 import 变量;		from .. import 模块伍;		from ..模块陆 import 变量;
#同一文件夹中的模块伍		#同一文件夹中的模块陆			#父文件夹中的模块伍			#父文件夹中的模块陆
import 模块壹 as 别名壹;
from 目录零 import 模块壹 as 别名壹
别名壹 = __import__("模块壹")
```
```python
#第一次import某模块时，会执行该文件所有顶层语句
#第一次import某目录时，会执行该目录中的__init__.py的语句
#from，在导入者中创建新变量，引用模块中同名变量。import的模块内对象是共享的。
#目录壹必须是模块搜索路径中的子目录。目录壹和子目录贰内都必须包含__init__.py这个文件。若需要父目录等，则需加入 sys.path.append("..")
```
```python
模块壹中变量命名为 _变量贰 ，则 from 模块 import * 时不会被导入
模块壹顶层写  _all_=["变量壹名","变量贰名"]，则 from 模块壹 import * 时只会导入这几个变量
```
```python
#本模块命名空间 __name__ 。当本模块是顶层程序时值为 "__main__" ，当本模块被作为import模块时值为 模块壹。	#用于自我检测区分工作角色
import builtins				#内置作用域
#内置函数命名空间是 __builtins__
```

```python
import imp
imp.reload(某模块)		#重新载入某模块
```

### 文件布局

```python
module文件夹中：

	文件test1.py：
		def test2():
			print("test3")
	
	文件setup.py：
		from distutils.core import setup
		setup(
				name	= 	"test4",
				version	=	"1.0.0",
				py_modules	=	["test1"],
			)
	
	执行：
		python setup.py sdist			#构建一个发布文件
		python setup.py install			#将发布安装到Python本地副本中
	
	module\dist文件夹中出现test4-1.0.0.zip

工作文件夹中：

	文件main.py:
		import test1
	
		test1.test2()
```

