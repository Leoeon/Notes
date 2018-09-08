# Pytorch笔记

## 张量

### 纯张量

```python
纯张量 = 张量初始化
纯张量.size()
```
不同初始化方法
```python
torch.Tensor(第一维长度,第二维长度)			#Tensor默认为FloatTensor。有其他类型
torch.Tensor([0.1, 1.0, 0.0001])
torch.randn(第一维长度,第二维长度)
torch.zeros(第一维长度,第二维长度)
torch.ones(第一维长度,第二维长度)
torch.eye()
torch.linspace(初值,末值,个数)				#等差数列
```

### 张量
```python
张量 = torch.autograd.Variable(
	纯张量,
	requires_grad=True,		## 该张量需要求导。输入存在reqires_grad则输出为requuires_grad
	volatile=True )			## 该张量仅用于推断不训练。输入存在violate则输出为violate
张量.data	#返回纯张量
张量.grad	#返回导数张量
```
整个图在每次迭代时从头开始重新创建
对生成目标张量过程中所有张量求导：$\sum_i \frac{d(目标张量[i]*倍数[i])}{d(张量)}$
新导数累加到旧导数上

```python
目标张量.backward(
	倍数,								#倍数维度需与目标张量维度一致
	retain_variables=True)				#不释放内存，允许下一次目标张量可求导
某系统.zero_grad()						#清空某系统中所有参数导数，如 模型对象、优化函数对象
```

### 性质

#### 相互转换

##### numpy
```python
纯张量    = torch.from_numpy( Numpy张量 )
Numpy张量 = 纯张量.numpy()
```

##### CUDA
除复制外，不支持跨GPU操作
```python
（纯）CUDA张量 = （纯）张量.cuda(GPU编号)	#将纯张量复制到GPU上
（纯）张量 = （纯）CUDA张量.cpu()			#将纯张量复制到CPU上
with torch.cuda.device(GPU编号):			#在范围内改变默认GPU编号
torch.cuda.is_available()
（纯）CUDA张量.get_device()					#返回GPU编号
```

#### 改变张量大小
```python
新张量 = 旧张量.view(2,3)                  #保持内存存储情况，仅改变张量行列数。不改变原张量。原张量与新张量共享内存
                                         #旧张量.view(-1,3)：第一维大小由其他维决定
新张量 = 旧张量.unsqueeze(维度)             #新加一维
纯张量.resize_(2,3)                       #保持内存存储情况，仅改变张量行列数。可改为空间更大更小
                                         #对 张量.data.resize_(2,3) 即可改变张量大小。张量.grad大小会保持原样，但依旧可正常工作
新张量 = 旧张量.transpose(2,4)             #保存内存存储情况，将第2维与第4维转置
新张量 = torch.cat([旧张量列表],dim=维度)   #将多个旧张量沿指定维度拼合为新张量
新张量 = torch.stack([旧张量列表],dim=维度) #将多个旧张量新增指定维度拼合为新张量
```
#### 数学运算
```python
张量壹.dot(张量贰)	或	torch.dot(张量壹,张量贰)         #所有元素两两相乘后求和
矩阵壹.mv(向量贰)	或	torch.mv(矩阵壹,向量贰,输出向量)      #矩阵乘向量
矩阵壹.mm(矩阵贰)	或	torch.mm(矩阵壹,矩阵贰,输出矩阵)      #矩阵乘矩阵
张量.clamp(min=小截断,max=大截断)                         ## if x<小截断: return 小截断;	if 小截断<x<大截断: return x;	if 大截断<x: return 大截断

张量.clone()
```




## 全流程

### 模型

#### 基本格式

##### 含参模型

```python
模型对象 = 含参模型(参数)
输出张量 = 模型对象(输入张量)
```
```python
class 自定义含参模型(torch.nn.Module):
	def __init__(self,参数):
		super(自定义模型,self).__init__()
		self.含参模型对象 = 含参模型				## 欲优化张量用 torch.nn.Parameter 而非 torch.autograd.Variable，方可加入parameters()列表
	def forward(self,输入张量):
		张量贰 = self.含参模型对象(输入张量)
		张量叁 = 无参模型(张量叁)
		return 张量叁
```
```python
模型对象.parameters()		#模型对象所有参数列表
```

##### 无参模型
```python
输出张量 = 无参模型(输入张量)
```
```python
class 自定义无参模型(torch.autograd.Function):
	def forward(self, 输入张量):
		self.save_for_backward(输入张量)
		return 输出张量
	def backward(self, 输出张量导数):
		输入张量, = self.saved_tensors
		return 输入张量导数
```

#### 预设

##### 含参模型
```python
torch.nn.Linear(输入维度,输出维度)							#全连接
torch.nn.Conv2d(输入维度,输出维度,卷积核边长)				#四维张量：样本数*卷积核数*高*宽

#损失函数亦为一种模型
torch.nn.MSELoss()
torch.nn.CrossEntropyLoss()

torch.nn.Sequential(										#顺序模型
	模型壹,
	模型贰)
```

##### 无参模型
```python
输出张量 = torch.nn.functional.relu(输入张量)
输出张量 = torch.nn.functional.max_pool2d(输入张量,(池化第一维大小,池化第二维大小))
```

### 流程

#### 模型训练流程
```python
优化函数对象 = 优化函数(待优化参数列表)							#待优化参数列表：如 模型对象.parameters()
```
##### 法一
```python
for :
	输出张量 = 模型对象(输入张量)	或	输出张量 = 无参模型(输入张量)		#张量第一维必须为batch
	模型对象.zero_grad()	或	优化函数对象.zero_grad()					#清空其中所有参数导数
	输出张量.backward()
	优化函数对象.step()														#优化更新参数
```

##### 法二
```python	
for :
	def 闭包():
		输出张量 = 模型对象(输入张量)	或	输出张量 = 无参模型(输入张量)	#张量第一维必须为batch
		模型对象.zero_grad()	或	优化函数对象.zero_grad()				#清空其中所有参数导数
		输出张量.backward()
		return 输出张量
	优化函数对象.step(闭包)
```

#### 预设优化函数
```python
torch.optim.SGD(待优化参数列表, lr=1e-3, momentum=0, weight_decay=0, dampening=0, nesterov=False)
torch.optim.Adam(待优化参数列表, lr=1e-3, eps=1e-8)
```



## 其他

### 数据集
```python
## [( 输入1,输出1), (输入2,输出2), ... ]
数据集 = torch.utils.data.TensorDataset( data_tensor=输入张量集, target_tensor=输出张量集 )

## [ (输入batch1,输出batch1), (输入batch2,输出batch2), ... ]
分批数据集 = torch.utils.data.DataLoader(     
	dataset=数据集,
	batch_size=批大小,
	drop_last=True,							## 若数据集无法被批大小整除，则丢弃最后的剩余样本
	shuffle=True,							## 是否随机打乱顺序
	sampler=采样方法,
	batch_sampler=采样方法,
	collate_fn=,
	num_workers=子进程读取数据的线程数,		## 0即为仅主进程
	pin_memory= )
```

### 钩子
```python
def 张量钩子函数(原本梯度):
	其他代码
	return 新梯度											#系统会用 新梯度 替代 原本梯度。若无return，则保持原本梯度不变
def 模块钩子函数(self,输入,输出):
	其他代码
	#不可改原本梯度。不可改变输入张量、输出张量
    
钩子句柄 = 张量.register_hook(张量钩子函数)					#当 某目标张量.backward() 经过本张量时，求导后执行该钩子函数
钩子句柄 = 模型对象.register_forward_hook(模型钩子函数)		#当网络前向传播经过该模块时，执行该钩子函数
钩子句柄 = 模型对象.register_backward_hook(模型钩子函数)	#当网络反向传播经过该模块时，执行该钩子函数
#按照注册钩子函数的顺序，依次执行钩子函数
钩子句柄.remove()				#取消该钩子
```



### 保存加载
只保存和加载模型参数
```python
torch.save(旧模型.state_dict(),路径)
新模型 = 网络(参数)
新模型.load_state_dict(torch.load(路径))
```

保存和加载整个模型（不推荐）
```python
torch.save(旧模型,路径)
新模型 = torch.load(路径)
```

