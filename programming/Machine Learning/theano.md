#theano笔记

Author:	Leoeon    
Date:	2016.03.31

```
import theano.tensor as T
```


## 1 变量
```
变量壹 = T.?XXXX("变量名壹")			#定义变量
	XXXX::	scalar:(), vector:(False,), row:(True,False), col:(False,True), matrix:(False,False), tensor3:(False,False,False), tensor4:(False,False,False,False)
	?::		无, b:int8, w:int16, i:int32, l:int64, f:float32, d:float64, c:complex64, z:complex128
三维张量=T.TensorType(broadcastable=(False,False,False),dtype="float32")
变量叁 = 三维张量("变量名叁")
变量贰壹,变量贰贰 = T.?XXXX(2)	或	变量贰壹,变量贰贰 = T.?XXXX("变量名贰壹","变量名贰贰")	#一次性定义多个相同类型变量
	XXXX::	scalars, vectors, rows, cols, matrices
T.as_tensor_variable( 原python变量, name=None, ndim=None )		#将原python变量转为theano_tensor变量	
```
```
常量壹 = T.arange(9).reshape((3,3))	
常量壹.eval()					#输出值
常量壹[常量壹>3].eval()
```
```
共享变量壹 = theano.shared(初始值)
theano.function参数中加入 updates=[(共享变量壹,新表达式壹),(共享变量贰,新表达式贰)]		#每次调用运算时都对共享变量进行更新
共享变量壹.get_value()
共享变量壹.set_value(值)
```


## 2 函数
```
函数壹 = 变量壹 + T.exp(变量贰) + 变量叁.内置函数(形参)
	对逐个元素进行运算：
		+ - * / ** // %		# 低维与高维张量运算时，在低维张量在最高维增补'x'。'x'维复制切片成高维张量
		T.exp/log/log2/log10(变量)		T.sqrt/sqr(变量)	T.abs_(变量)	T.erf/erfc(变量)	T.gamma/gammaln(变量)
		T.zeros_like(变量)	T.ones_like(变量)	T.identity_like(变量)	T.fill(变量,值)		#所有元素皆为某值的相同大小张量
	张量运算：
		T.dot(变量壹,变量贰)		#向量、矩阵乘法	
		T.outer(变量壹,变量贰)
		T.stack([变量壹,变量贰])	#将多个相同维数相同大小变量作为切片叠成更高维变量
	内置函数：
		最大值max(min)、最大值位置argmax(argmin)、最大小值差p2p、求和sum、求积prod、均值mean、方差var、标准差std (axis=(0,2))	#对第0维、第2维的所有元素进行内置函数操作。不填 axis= 则为对张量所有元素
		fill(值)			#张量所有元素皆为该值
		dimshuffle((2,0,'x',1))		#转置为按第2、0、1维排列的张量，'x'表示增加一个(如同True的)零维
	求导：
		T.grad( 标量函数壹, [自变量壹,自变量贰] )	#返回函数壹对各自变量的偏导函数
		theano.gradient.hessian( 标量函数壹, 向量自变量壹 )
		theano.gradient.jacobian( 向量函数壹, 向量自变量壹 )
	分支：
		T.switch( 判断条件, 正确返回值, 错误返回值 )
		IFELSE.ifelse( 判断条件, 正确返回值, 错误返回值 )		#import theano.ifelse as IFELSE
```
```		
运算甲 = theano.function( inputs=[ 变量壹, theano.In(变量贰,value=2,name='y_by_name') ], outputs=[函数壹,函数贰] )			#指明输入变量、输出函数
运算结果甲 = 运算甲( 3, y_by_name=4 )							#计算值
运算结果乙 = 运算乙( [[1,2],[3,4]], [[5,6],[7,8]] )
```
```	
函数值壹 = 函数壹.eval({变量壹:3, 变量贰:4}) 		#等价
```
```
theano.function参数中加入 givens=[(原变量壹,新表达式壹),(原变量贰,新表达式贰)]	或	givens={原变量壹:新表达式壹,原变量贰:新表达式贰}    
	#在本运算中，用新表达式替代原变量。运算外不改变原变量
```
```
theano.function参数中加入 mode = 'FAST_COMPILE' / 'FAST_RUN' / 'DebugMode' / 
```

	
## 3 循环
```	
结果序列, 更新共享变量 = theano.scan(
	fn = 函数
		#def 函数 (sequances参数，output_info参数，no_sequences参数):
			return output_info输出列表, 更新共享变量, theano.scan_module.until(停止条件)
	sequences = [迭代参数壹序列, 迭代参数贰序列],							#序列第一维为迭代次数
	outputs_info = [初始回溯壹序列, 初始回溯贰序列],
	non_sequences = [每次迭代固定输入壹, 每次迭代固定输入贰],
	n_steps = 迭代次数,														#与sequences择一使用
	strict=True)															#限定no_sequences参数都在函数参数中，加速
		#参数壹序列可写为：dict(initial=参数, taps=下标列表。如dict(initial=X, taps=[-2,1] 意为 X[t-2]、X[t+1]
运算 = theano.function( inputs=[], outputs=结果, updates=更新共享变量 )			#指明输入变量、输出函数
	# 输入逻辑结构为 [第0次sequence, 第1次sequence, 第2次sequence, ...]
	# 输出逻辑结构为 [第-2次初始回溯, 第-1次初始回溯, 第0次outputs_info, 第1次outputs_info, 第2次outputs_info, ...] 
	  结果序列为 [第0次outputs_info, 第1次outputs_info, 第2次outputs_info, ...]
	  循环每次函数时，以本次为t，读入t-n次历史数据
	  实际调用时，传入第-n次初始回溯为实参
```	


## 4 随机数	
```	
from theano.tensor.shared_randomstreams import RandomStreams		#CPU
from T.sandbox.rng_mrg import MRG_RandomStreams as RandomStreams	#GPU
随机流 = RandomStreams( seed=种子 )
随机函数壹 = 随机流.随机函数( (第一维长度,第二维长度) )
	#随机函数：	均匀分布uniform、标准正态分布normal
运算甲 = theano.function( [], 随机函数壹, no_default_updates=False )			#若no_default_updates=True则每次 运算甲() 不更新随机数
```



## 5 IO
```
print(theano.pp(函数壹))				#输出函数壹：(变量名壹 + exp(变量名贰))
theano.printing.debugprint(函数壹)
```
