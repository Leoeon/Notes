# Python 序列化 笔记

## pickle

自动用bytes字符串将存储在文件中。可以直接存任何内容，无需手动转成字符串
```python
import pickle
with open("文件名","wb") as 文件壹:
	pickle.dump(要存内容,文件壹)
	#或
	文本流=pickle.dumps(要存内容);	文件壹.write(文本流)
with open("文件名","rb") as 文件贰:
	读内容变量 = pickle.load(文件贰)
```

## shelve
将内容以字典形式存在文件中
```python
import shelve
文件壹 = shelve.open("文件名")
文件壹[键] = 要存内容			#可对文件壹进行各种字典处理。类
文件壹.close
```

## json
```python
import json
JSON标准字符串 = json.dumps(PYTHON对象)		#将PYTHON对象转换为符合JSON标准的字符串
PYTHON对象 = json.loads(JSON标准字符串)		#将符合JSON标准的字符串转换为PYTHON对象
	#可选参数：	sort_keys=True		#对dict对象排序
	#          	indent=缩进空格数	#存储str时含缩进
	#          	separators=( "替换,的字符串", "替换:的字符串" )
	#          	skipkeys=True		#当key无法转为str时忽略。默认skipkeys=False，若key无法转为str则会报错。
	#          	default=转换方案
```

## xml
```python
import xml.etree.ElementTree as ET

树 = ET.ElementTree(file="文件名")

根元素 = tree.getroot()
子元素贰 = 父元素[2]					#第2个子元素
for 子元素 in 父元素:					#遍历父元素的所有子元素
for 元素 in 树.iter(tag="元素名"):		#递归遍历所有该名元素。默认参数为所有元素
第一个匹配元素 = tree.find("XPath匹配")
所有匹配元素列表 = tree.findall("XPath匹配")
for elem in tree.iterfind("XPath匹配"):

元素名 = 元素.tag
元素属性 = 元素.attrib
元素内容 = 元素.text

树.write(输出文件)						#输出到屏幕上则写sys.stdout
```
