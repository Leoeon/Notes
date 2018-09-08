# Python 操作系统 笔记

## 系统

### sys
```python
import sys		#标准库模块
sys.argv			#命令行参数列表，argv[0]必然是要运行的脚本名称
sys.path			#输入模块的目录名列表
sys.stdout			#标准输出即屏幕为
sys.modules			#已加载的模块列表
```

### os
```python
import os
os.getcwd()					#得到当前工作目录
os.chdir("新路径")			#移动到新的文件夹。若出错则抛出FileNotFoundError
os.mkdir("文件夹名")		#新建文件夹
os.remove("文件名")			#删除文件
os.rename("旧文件名","新文件名")		#改名
os.listdir("文件夹")		#返回指定目录下的所有文件和目录名。默认参数为当前文件夹
os.path.isfile("路径")		#检验给出的路径否为文件
os.path.isdir("路径")		#检验给出的路径否为目录
os.path.exists("路径")		#检查路径是否存在
os.system("命令")			#在操作系统中运行命令
os.popen("命令")			#在操作系统中运行命令。会返回类文件对象，用于访问标准输入、输出。如os.popen("命令").read()等文件操作
os.sep						#返回本系统路径分隔符
os.name						#依赖操作系统模块的名字
os.environ	或	os.environ["PATH"]	#环境变量
os.cpu_count()				#核数
```

### getopt
```python
import getopt				#一般用于对命令行参数进行分析
开关对列表,剩余项列表 = getopt.getopt( 分析列表, "短开关说明", [长开关说明] )
	#分析列表一般为 命令行参数列表 sys.argv[1:]
	 "短开关说明" = "hi:o:" 表明开关有 -h、-i 参数、-o 参数
	 [长开关说明] = ["version=","help"] 表明开关有 --version=参数、--version 参数、--help
	 将分析列表从左往右读取，遇到第一个不满足的项就停止
	#例：分析列表 = [ "-i","iii", "-h", "--version","vvv", "-i","jjj", "sdf", "asdf", "-o","oo" ]
	     开关说明 = "hi:o:"
		 长开关说明 = ["version=","help"]
	     则 开关对列表 = [ ("-i","iii"), ("-h",""), ("--version:","vvv"), ("-i","jjj") ]
		    剩余项列表 = ["sdf","asdf","-o","oo"]
```

## 进程/线程 

python中因GIL存在，多线程仅用于I/O加速

### os
os多线程由Unix的fork封装而来，故不可用于windows
```python
os.fork()	#产生子线程。子线程返回0，父线程返回子进程的ID。若创建失败，将抛出OSError异常
os.getpid()
```

### Pool
```python
from multiprocessing       import Pool					#多进程
from multiprocessing.dummy import Pool					#多线程
with Pool(进/线程数) as 并行实例:						#进/线程数 默认参数为本机最大核数
	返回结果列表 = 并行实例.map( 函数名, 实参列表 )		#原意为“列表中的每个元素为一次实参”来执行函数，现分配至各进/线程执行
```

### Parallel
```python
from threading       import Thread  as Parallel
from multiprocessing import Process as Parallel

子线/进程壹 = Parallel( target=函数名, args=(实参壹,实参贰) )
子线/进程壹.start()					#开始执行 函数名(实参壹,实参贰)
子线/进程壹.join()					#主线/进程等待子线/进程壹完成

class 自定义类(Parallel):
	def __init__(self,形参壹,形参贰):
		Parallel.__init__(self)
	def run(self):
		#自定义并行时要执行的内容
实例化 = 自定义类(实参壹,实参贰)
实例化.start()
```

### threading/multiprocessing
```python
线程锁 = threading.Lock()
进程锁 = multiprocessing.Lock()
线/进程锁.acquire()				#上锁。若已被其它线程锁上则阻塞等待至解锁
线/进程锁.release()				#解锁
with 线/进程锁:					#with范围内上锁，with结束时解锁

阻塞 = threading.Barrier(线/进程数目)
阻塞 = multiprocessing.Barrier(线/进程数目)
阻塞.wait()						#所有线/进程都执行到此再同时继续

#用定向管道在进程间传输数据。数据先入先出
双向管道 = multiprocessing.Pipe()
双向管道[0].send(发送数据)			#一方在管道一头发送数据
接受数据=双向管道[1].recv()			#另一方在管道另一头接受数据
	#0、1反之亦然
单向管道 = multiprocessing.Pipe(duplex=False)	#只能1发0收

#用共享管道在进程间共享数据。数据先入先出
共享管道 = multiprocessing.Queue(可存数据最大数目)
共享管道.put(发送数据)
接收数据 = 共享管道.get()

#共享内存
标量 = multiprocessing.Value('类型', 标量)		标量.value	#标量的值
向量 = multiprocessing.Array('类型', 向量)		向量[索引]		#向量的值
#类型：i整形，d双精度

#服务器共享内存
服务器 = multiprocessing.Manager()
标量 = 服务器.Value('类型', 标量)
向量 = 服务器.Array('类型', 向量)
包装类型 = 服务器.包装类型()		#包装类型有：list、dict、Namespace、Lock、RLock、Semaphore、BoundedSemaphore、Condition、Event、Barrier、Queue、Value、Array
```

### subprocess
```python
import subprocess		#shell子进程管理

#外部命令：	subprocess.???( ["ls","-l"] )	或：subprocess.???( "ls -l", shell=True )

#包装。父进程等待子进程完成
subprocess.call(外部命令)			#返回退出信息
subprocess.check_call(外部命令)		#返回0
subprocess.check_output(外部命令)	#返回子进程向标准输出的输出结果

#父进程不等待，继续执行代码
子进程壹 = subprocess.Popen(外部命令)
子进程壹.wait()					#父进程等待子进程完成
子进程壹.poll()					#检查子进程状态
子进程壹.kill()					#终止子进程
子进程壹.send_signal()			#向子进程发送信号
子进程壹.terminate()			#终止子进程
子进程壹.pid					#子进程进程ID

子进程叁 = subprocess.Popen( 外部命令, stdin=子进程贰.stdout, stdout=subprocess.PIPE )		#管道。子进程贰的输出文本通过管道输入子进程叁，子进程叁的输出文本再流入管道中
子进程叁输出文本 = 子进程叁.communicate()			#若stdout=subprocess.PIPE，则从PIPE中读取子进程贰输出的文本

子进程肆 = subprocess.Popen( 外部命令, stdin=subprocess.PIPE )		#子进程肆阻塞，等待输入
子进程肆.communicate(输入文本)
```
