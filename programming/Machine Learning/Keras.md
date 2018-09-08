# 模型

## 基本模型

### 抽象层

```python
from keras.layers import 抽象层
```

```python
网络结构 = 抽象层( 输出维度, input_shape=(第一维大小,第二维大小), name="本层命名", 其他参数 )
# 或
网络结构 = 抽象层( 输出维度, batch_input_shape=(固定batch大小,第一维大小,第二维大小), name="本层命名", 其他参数 )
```
部分层的输出维度可自动推断，则可省略  
部分非首层的输入维度可自动推断，则可省略

### Sequancial
```python
顺序模型 = keras.models.Sequential()
顺序模型.add(网络结构)
```


### 泛型模型
```python
输出张量 = 网络结构(输入张量)
泛型模型 = keras.models.Model(input=[总输入张量壹,总输入张量贰], output=[总输出张量壹,总输出张量贰])
```
所有模型都属于网络结构，可被调用，重用结构及权重参数



# 流程

## 编译
```python
模型.compile(
	optimizer    = 优化器,
	loss         = {"层名壹":损失函数壹, "层名贰":损失函数贰},
	loss_weights = {"层名壹":损失函数壹权重, "层名贰":损失函数贰权重},
	metrics      = {"层名壹":指标壹, "层名贰":指标贰} )
```
预设优化器：
- 'rmsprop'
- 'adagrad'

预设损失函数：
- 'categorical_crossentropy'
- 'binary_crossentropy'
- 'mse'

预设指标：
- 'accuracy'

## 训练
```python
模型.fit(
	输入数据,                                     # 单输入数据为numpy array，多输入数据为numpy array的list
	目标标签,                                     # 单输出数据为numpy array，多输出数据为numpy array的list
	batch_size       = 进行一次梯度下降所用的样本数,
	nb_epoch         = 训练的论数, 
	verbose          = 日志显示,                  # 0为不在标准输出流输出日志信息，1为输出进度条记录，2为每个epoch输出一行记录
	callbacks        = [回调函数], 
	validation_split = 验证集/(验证集+训练集),    # 挑出一部分比例作为训练集。0~1 间浮点数
	validation_data  = None, 
	shuffle          = True, 
	class_weight     = None, 
	sample_weight    = None)
```



# 其他

## 预设抽象层

### Input
```python
输出张量 = keras.engine.topology.Input(shape=(输入维数),其他输入参数)
```

### 连接层

#### 全连接层
```python
网络结构 = keras.layers.core.Dense(
	输出维度, 
	init                 = 初始化方法, 
	activation           = 激活函数, 
	weights              = 权值,         # [(输入维度,输出维度)的numpy array权重矩阵, (输出维度,)的numpy array偏置向量] 
	W_regularizer        = None,         #权重正则项
	b_regularizer        = None,         #偏置向量正则项
	activity_regularizer = None,         #输出正则项 
	W_constraint         = None,         #权重约束项
	b_constraint         = None,         #偏置约束项
	bias                 = True,         #是否包含偏置向量
	)  
```

#### 卷积层
```python
网络结构 = keras.layers.convolutional.Convolution2D(
	nb_filter            = 卷积核数目, 
	nb_row               = 卷积核行数, 
	nb_col               = 卷积核列数, 
	init                 = 初始化方法, 
	activation           = 激活函数, 
	weights              = 权值, 
	border_mode          = 边界模式,			# "valid"、"same"、"full"
	subsample            = (1, 1), 
	dim_ordering         = 后端通道维, 			# "th"(3,128,128)、"tf"(128,128,3)
	W_regularizer        = None, 
	b_regularizer        = None, 
	activity_regularizer = None, 
	W_constraint         = None, 
	b_constraint         = None, 
	bias                 = True,
	)
```

### 非线性

#### Activation
```python
网络结构 = keras.layers.core.Activation(激活函数)
网络结构 = keras.layers.advanced_activations.LeakyReLU(alpha=0.3)
```


#### Dropout
```python
网络结构 = keras.layers.core.Dropout(断开概率)
```

#### Batch Normalization
```python
keras.layers.normalization.BatchNormalization(
	epsilon=1e-06, 
	mode=0, 
	axis=-1, 
	momentum=0.9, 
	weights=None, 
	beta_init='zero', 
	gamma_init='one')
```


### 张量变换

#### Merge
```python
输出结构 = keras.engine.topology.Merge( [输入结构壹, 输入结构贰], mode=合并函数 )
输出张量 = keras.engine.topology.merge( [输入张量壹, 输入张量贰], mode=合并函数 )
```
预设mode:		
- 'sum':逐元素相加	
- 'concat':张量串联，可以通过提供concat_axis的关键字参数指定按照哪个轴进行串联	
- 'mul'：逐元素相乘	
- 'ave'：张量平均 	
- 'dot'：张量相乘，可以通过dot_axis关键字参数来指定要消去的轴	
- 'cos'：计算2D张量（即矩阵）中各个向量的余弦距离	

可用 ```mode=自定义合并函数``` 替代

#### 转换维数

将输入层转为新的维数（不包含batch维）
```python
网络结构 = keras.layers.core.Reshape((新第一维大小,新第二维大小))
```

将多维数据压为一维（不包含batch维）
```python
网络结构 = keras.layers.core.Flatten()
```

增加一维，将输入层重叠多次而得
```python
网络结构 = keras.layers.core.RepeatVector(重复次数)
```



## 存储
```python
旧模型.save("文件名")
新模型 = keras.models.load_model("文件名")
网络参数 = 模型.get_weights()
仅网络结构 = model.to_json()
```
