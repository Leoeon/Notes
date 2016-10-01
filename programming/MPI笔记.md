#MPI笔记

Author:	Leoeon    
Date:	2014.07.30


## 1 基本框架

```
#include<mpi.h>
int main( int argc, char *argv[] )
{
	MPI_Init( &argc, &argv );			// MPI_Init( NULL, NULL );
	//全局通信子：MPI_COMM_WORLD	仅包含本进程的通信子：MPI_COMM_SELF
	int comm_sz;	MPI_Comm_size( 通信子, &comm_sz );		//进程总数量
	int my_rank;	MPI_Comm_rank( 通信子, &my_rank );		//本进程号		
	MPI_Finalize();
	return 0;
}
```

## 2 点对点通信

### 2.1 阻塞型通信函数

```
MPI_Send( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子 );
	//可能阻塞可能缓冲 点对点发送消息
MPI_Ssend( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子 );			
	//阻塞 安全的点对点发送消息
MPI_Rsend()
	//？？？就绪
MPI_Recv( void*接收消息指针, int 要接收的数据量, 消息数据类型, int 发送方进程号, int 接收标签, 通信子, 状态);	
	//阻塞 点对点接收消息
MPI_Sendrecv( void*发送消息指针, int 要发送的数据量, 发送消息数据类型, int 接收方进程号, int 发送标签, void*接收消息指针, int 要接受的数据量, 接收消息数据类型, int 发送方进程号, int 接收标签, 通信子, &状态 );	
	//两进程间相互分别执行一次阻塞式消息发送与一次消息接收
MPI_Sendrecv_replace( void*收发消息指针, int 要收发的数据量, 消息数据类型, int 接收方进程号, int 发送标签, int 发送方进程号, int 接收标签, 通信子, &状态 );	
	//在同个消息处，两进程间相互分别执行一次阻塞式消息发送与一次消息接收
```

成功接收必要条件：    
- 消息的数据类型必须相同    
- 要接受的数据量≥要发送的数据量    

预定义的MPI数据类型
```
	MPI_CHAR         	MPI_SHORT         	MPI_INT     	MPI_LONG         	MPI_LONG_LONG	
	MPI_UNSIGNED_CHAR	MPI_UNSIGNED_SHORT	MPI_UNSIGNED	MPI_UNSIGNED_LONG	
	MPI_FLOAT	MPI_DOUBLE	MPI_LONG_DOUBLE	
	MPI_BYTE	MPI_PACKED
	MPI_FLOAT_INT	MPI_LONG_INT	MPI_DOUBLE_INT	MPI_SHORT_INT	MPI_2INT	MPI_LONG_DOUBLE_INT		// struct{a,b}
	MPI_Type_size( MPI数据类型, int*MPI数据类型字节长度 );		//得到MPI数据类型字节长度
```

发送方进程号 可替换为 MPI_ANY_SOURCE 或 MPI_PROC_NULL    
接收方进程号 可替换为 MPI_PROC_NULL    

接收标签 可替换为 MPI_ANY_TAG    

```
MPI_Status 状态;				//在MPI_Recv后检测	
发送者 = 状态.MPI_SOURCE;
接收标签 = 状态.MPI_TAG;
错误码 = 状态.MPI_ERROR;
MPI_Get_count( &状态, 消息数据类型, &接收数据量);    //获取接收数据量
```
状态 可替换为 MPI_STATUS_IGNORE

	
### 2.2 缓冲通信

```
//？？？与非阻塞通信区别？
MPI_Buffer_attach( void*缓冲区指针, int 缓冲区长度 );
	//指定缓冲区。缓冲区长度 = MPI_BSEND_OVERHEAD + MPI数据类型字节长度 * 数据量
MPI_Bsend( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子 );				//点对点发送消息
	//将发送消息拷贝到事先指定的缓冲区后立即返回，系统后台发送
MPI_Buffer_detach( void*缓冲区指针, int*缓冲区长度 );
	//撤销缓冲区。输出被撤销的缓冲区地址与长度
MPI_Probe( int 发送方进程号, int 接收标签, 通信子, &状态 );
	//阻塞等待消息到达
```		
	
### 2.3 非阻塞通信		

```
MPI_Request 请求;
MPI_Isend( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
	//非阻塞点对点发送，MPI系统后台发送消息
MPI_Ibsend( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
MPI_Irsend( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
MPI_Issend( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
MPI_Irecv( void*接收消息指针, int 要接收的数据量, 消息数据类型, int 发送方进程号, int 接收标签, 通信子, &请求 );
	//非阻塞点对点接受，MPI系统后台接受消息
MPI_Request_free( &请求 );
	//？？？等待传输完成，释放请求所占资源
MPI_Iprobe( int 发送方进程号, int 接收标签, 通信子, int*完毕判断, &状态 );
	//探测是否有消息到达。立刻返回。若传输完毕则“完毕判断=true”，若传输未完毕则“完毕判断=false”
```
```					
MPI_Test( &请求, int *完毕判断, &状态 );
	//检测指定的通信请求。若传输完毕则“完毕判断=true”，若传输未完毕则“完毕判断=false”。
MPI_Testall( int 请求数目, 请求列表, int*完毕判断, 状态列表 );
	//检测通信请求列表。若全部传输完毕则“完毕判断=true”，若存在传输未完毕则“完毕判断=false”。
MPI_Testany( int 请求数目, 请求列表, int*完毕者下标, int*完毕判断, 状态列表 );
	//检测通信请求列表。若存在传输完毕则“完毕判断=true”，若全部传输未完毕则“完毕判断=false”。
MPI_Testsome( int 请求数目, 请求列表, int*完成数目, 完毕者下标列表, 状态列表 );
	//检测通信请求列表。“完成者下标列表”的前“完成数目”个值为完毕请求在请求列表中的下标。
	//立即返回。
	  接收消息的状态同MPI_Recv，发送消息的状态未定义
```
```	  
MPI_Wait( &请求, &状态);
	//等待通信请求传输完毕后返回。
MPI_Waitall( int 请求数目, 请求列表, 状态列表 );
	//等待通信请求列表全部传输完毕后返回。
MPI_Waitany( int 请求数目, 请求列表, int*完毕者下标, 状态列表 );
	//等待通信请求列表任一传输完毕后返回。
MPI_Waitsome( int 请求数目, 请求列表, int*完成数目, 完毕者下标列表, 状态列表);
	//？？？
	//接收消息的状态同MPI_Recv，发送消息的状态未定义
```		
	
### 2.4 持久通信

```
MPI_Send_init( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
	//创建非阻塞型持久消息发送请求
MPI_Bsend_init( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
MPI_Rsend_init( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
MPI_Ssend_init( void*发送消息指针, int 要发送的数据量, 消息数据类型, int 接收方进程号, int 发送标签, 通信子, &请求 );
MPI_Recv_init( void*接收消息指针, int 要接收的数据量, 消息数据类型, int 发送方进程号, int 接收标签, 通信子, &请求 );
	//创建非阻塞型持久消息接收请求
MPI_Start( &请求 );
	//启动通信。每次相当于调用一次 MPI_Isend 或 MPI_Irecv
MPI_Startall( int 请求数目, 请求列表 );
	//启动一组通信
```	
	
## 3.集合通信

### 3.1 阻塞

```
MPI_Barrier( 通信子 );
	//阻塞同步
```

### 3.2 广播

```
MPI_Bcast( void*广播消息指针, int 数据量, 数据类型, int 广播源进程号, 通信子 );														
	//将一个进程的消息发送到所有进程同名位置上
```
	
### 3.3 汇总与散射

每进程数据量必须相同

```
MPI_Gather( void*发送消息指针, int 发送每进程数据量, 发送数据类型, void*聚集消息指针, int 接收每进程数据量, 接收数据类型, int 聚集汇进程号, 通信子 );		
	//按块划分，将所有进程消息作为分量汇聚到一个进程上
MPI_Allgather( void*发送消息指针, int 发送每进程数据量, 发送数据类型, void*聚集消息指针, int 接收每进程数据量, 接收数据类型, 通信子 );				
	//按块划分，将所有进程消息作为分量汇聚到所有进程上
	//划分方式：块划分、循环划分、块——循环划分
MPI_Scatter( void*散射消息指针, int 发送每进程数据量, 发送数据类型, void*接收消息指针, int 接收每进程数据量, 接收数据类型, int 散射源进程号, 通信子 );		
	//按块划分，将一个进程的消息分量分发给所有进程
MPI_Alltoall( void*发送消息指针, int 发送每进程数据量, 发送数据类型, void*接收消息指针, int 接收每进程数据量, 接收数据类型, 通信子 );
	//进程i将第j块数据发送到进程j第i个位置
```

### 3.4 汇总与散射

每进程数据量可以不同

```
MPI_Gatherv( void*发送消息指针, int 发送每进程数据量, 发送数据类型, void*聚集消息指针, int*接收每进程数据量列表, int*接收每进程数据起始偏移值, 接收数据类型, int 聚集汇进程号, 通信子 );		
	//按块划分，将所有进程消息作为分量汇聚到一个进程上
MPI_Allgatherv( void*发送消息指针, int 发送每进程数据量, 发送数据类型, void*聚集消息指针, int*接收每进程数据量列表, int*接收每进程数据起始偏移值, 接收数据类型, 通信子 );		
	//按块划分，将所有进程消息作为分量汇聚到所有进程上	
MPI_Scatterv(  void*散射消息指针, int*发送每进程数据量列表, int*发送每进程数据起始偏移值, 发送数据类型, void*接收消息指针, int 每进程数据量, 接收数据类型, int 散射源进程号, 通信子 );		
	//按块划分，将一个进程的消息分量分发给所有进程
MPI_Alltoallv( void*发送消息指针, int*发送每进程数据量列表, int*发送每进程数据起始偏移值, 发送数据类型, void*接收消息指针, int*接收每进程数据量列表, int*接收每进程数据起始偏移值, 接收数据类型, 通信子 );
	//进程i将第j块数据发送到进程j第i个位置
```

### 3.5 归约操作
```
MPI_Reduce( void*输入消息指针, void*输出结果指针, int 要归约的数据量, 消息数据类型, 归约运算符, int 归约目标进程, 通信子 );	
	//将所有进程同名消息汇总处理,结果放至某个进程中
MPI_Allreduce( void*输入消息指针, void*输出结果指针, int 要归约的数据量, 消息数据类型, 归约运算符, 通信子 );				
	//将所有进程同名消息汇总处理,结果放至所有进程中
MPI_Scan( void*输入消息指针, void*输出结果指针, int 要归约的数据量, 消息数据类型, 归约运算符, 通信子 );
	//进程i输出结果为0~i个进程归约运算结果
//预定义的归约运算符
	MPI_MAX 求最大值	MPI_MIN 求最小值	MPI_MAXLOC 求最大值及其所在位置	MPI_MINLOC 求最小值及其所在位置
	MPI_SUM 求累加和	MPI_PROD 求累乘积
	MPI_LAND 逻辑与		MPI_LOR 逻辑或		MPI_LXOR 逻辑异或
	MPI_BAND 按位与		MPI_BOR 按位或		MPI_BXOR 按位异或
```
```		
MPI_Op 自定义归约运算符;
void 自定义函数名( void*输入消息指针, void*输出结果指针, int*要归约的数据量, MPI_Datatype*数据类型 );
	// for(int i=0; i!=要操作的数据量; ++i) 输出结果[i]=输入消息[i] op 输出结果[i]
MPI_Op_create( &自定义函数名, int 交换律判断, &自定义归约运算符 );
	//创建二目运算符
	//若满足交换律，则“交换律判断=true”。若不满足交换律，则“交换律判断=false”
MPI_Op_free( &自定义归约运算符 );
	//释放资源
```

## 4.派生数据类型

### 4.1 派生类型
```
MPI_Datatype 数据类型;

MPI_Type_contiguous( int 数据量, 旧数据类型, &新数据类型 );
	//同种旧数据类型数组
MPI_Type_vector( int 重复次数, int 每次重复数据长度, int 每次重复总长度, 旧数据类型, &新数据类型 );
	//同种旧数据类型周期性重复
	//例：XX...XX...XX：重复次数=3，每次重复长度=2，每次重复总长度=5
MPI_Type_indexed( int 数据块块数, int*每块数据长度, int*每块数据起始位置, oldtype, newtype);
	//同种旧数据类型数据块
	//例：..XXX...XXXXX..XX：数据块块数3，每块数据长度{3,5,2}，每块数据起始位置{2,8,15}
MPI_Type_create_struct( int 数据种类, int*每种数据个数, int*每种数据地址, int*每种数据类型, &新数据类型 );
	//多种旧数据类型
	//例：struct V{ int i_, double d_, int is_[3] };
	      数据种类3，每种数据个数{1,1,3}，每种数据地址{0,sizeof(int),sizeof(int)+sizeof(double)}，每种数据类型{MPI_INT,MPI_DOUBLE,MPI_INT}
	      数据地址亦可用：MPI_Get_address( &第i项, 第i项地址 ); 每种数据地址[i]={第i项地址-第0项地址}

MPI_Type_commit( &新数据类型 );
	//生成新类型
MPI_Type_free( &新数据类型 );
	//释放资源
```

### 4.2 打包

```
MPI_Pack( void*源数据地址, int 数据量, 数据类型, void*缓存区地址, int 缓存区最大长度, int*起始位置, 通信子 );
	//将数据打包至缓存区，以便进行一次性通信
	  第一次调用MPI_Pack前，“起始位置=0”，每次调用后函数将其改为指向尚未使用的起始位置
//进行发送/接受时，“发送/接受消息指针”为“缓存区地址”，“数据量”为最后一次调用MPI_PACK后的“起始位置”，“数据类型”为MPI_PACKED
MPI_Unpack( void*缓存区地址, int 缓存区最大长度, int*起始位置, void*解包数据地址, int 数据量, 数据类型, 通信子 );
	//将接收的数据进行解包
MPI_Pack_size( int 数据量, 数据类型, 通信子, int*数据长度 );
	//返回打包数据后的所占数据长度。因打包有额外信息，故由此估计所需缓存区最大长度
```	

## 5 进程组与通信子	

### 5.1 进程组

```
MPI_Group 进程组;

MPI_Group_size( 进程组, int*进程数目 );	
	//查询该进程组中进程数目
MPI_Group_rank( 进程组, int*秩 );
	//查询本进程在组里的秩
MPI_Group_compare( 进程组壹, 进程组贰, int*查询结果);
	//MPI_IDENT：两进程组进程集合相同、所有进程进程号相同
	  MPI_SIMILAR：两进程组进程集合相同，进程号不同 
	  MPI_UNEQUAL：两进程组进程集合不同

MPI_Group_difference( 大进程组, 小进程组, &补进程组 );
	//“补进程组”中的进程属于“大进程组”而不属于“小进程组”
MPI_Group_intersection( 进程组壹, 进程组贰, &交进程组 );
	//“交进程组”中的进程同时属于“进程组壹”与“进程组贰”
MPI_Group_union( 进程组壹, 进程组贰, &并进程组 );
	//“交进程组”中的进程属于“进程组壹”或“进程组贰”
MPI_Group_incl( 旧进程组, int 新组进程数目, int*新组进程在原组中秩列表, &新进程组 );
	//由旧进程组中的一部分进程创建新进程组
MPI_Group_excl( 旧进程组, int 排除进程数目, int*排除进程在原组中秩列表, &新进程组 );
	//由旧进程组中排除掉一部分进程创建新进程组
MPI_Group_range_incl( 旧进程组, int 范围数目, int 范围[][3], &新进程组 );
	//由旧进程组中的一部分进程创建新进程组。每一范围为{起点,终点(含),步长}
MPI_Group_range_excl( 旧进程组, int 排除范围数目, int 排除范围[][3], &新进程组 );
	//由旧进程组中排除掉一部分进程创建新进程组。排除范围为{起点,终点(含),步长}
MPI_Group_free( &进程组 );
	//释放资源
```

### 5.2 通信子

```
MPI_Comm 通信子;	

MPI_Comm_size( 通信子, int*进程数目 );
	//查询该通信子中进程数目
MPI_Comm_rank( 通信子, int*秩 );
	//查询本进程在通信子中的秩
MPI_Comm_compare()
	//MPI_IDENT：两通信子是同一个
	  MPI_CONGRUENT：两通信子进程集合相同、所有进程进程号相同
	  MPI_SIMILAR：两通信子进程集合相同，进程号不同 
	  MPI_UNEQUAL：两通信子进程集合不同
	  
MPI_Comm_dup( 旧通信子, &新通信子 );
	//复制通信子，但二者仅是属性相同，并不可相互通信
MPI_Comm_split( 旧通信子, int 颜色, int 关键字, &新通信子 );
	//将“旧通信子”分隔为多个“新通信子”。相同颜色进程被分到同一个新通信子中。按关键字大小排序决定在新通信子中的秩
	  若进程不属于任何新通信器，则“颜色=MPI_UNDEFINED”
MPI_Comm_free( &通信子 );
	//释放资源
```
	
### 5.3 结合
```
MPI_Comm_group( 通信子, &进程组 );
	//由“通信子”得到其“进程组”
MPI_Comm_create( 旧通信子, 新进程组, &新通信子 );
	//由“旧通信子”与“新进程组”生成“新通信子”
```

## 6.辅助拓扑

```
MPI_Topo_test( 通信子, int*拓扑类型 );
	//查询拓扑类型
	  MPI_CART：笛卡尔拓扑
	  MPI_GRAPH：图拓扑
	  MPI_UNDEFINED：其他
```

### 6.1 笛卡尔拓扑	

```
MPI_Cart_create( 旧通信子, int 维数, int*每维长度, int*每维周期性判断, int 重排判断, &笛卡尔通信子 );
	//创建笛卡尔拓扑结构
	  若第i维周期性，则“周期性[i]=true”。若第i维非周期性，则“周期性[i]=false”
	  若秩可重排序，则“重排判断=true”。若不可重排序，则“重排判断=false”
MPI_Dims_create( int 旧通信子进程数, int 维数, int*每维长度 );
	//自动生成各维建议长度，使得总乘积等于通信子进程数，且各维长度尽量接近
	//若输入时“每维长度[i]>0”，则维持该维长度。若输入时“每维长度[i]=0”，则可对其进行改变

MPI_Cartdim_get( 笛卡尔通信子, int*维数 );
	//返回笛卡尔拓扑维数
MPI_Cart_get( 笛卡尔通信子, int 维数, int*每维长度, int*每维周期性判断, int*每维坐标 );
	//返回本进程每维长度、每维周期性判断、每维坐标
MPI_Cart_rank( 笛卡尔通信子, int*每维坐标, int*秩 );
	//返回该坐标所在秩

MPI_Cart_shift( 笛卡尔通信子, int 维号, int 步长, int*源地址, int*目的地址 );
	//若想数据沿某维进行平移某步长，自动计算出由“源进程号”数据移至本进程，本进程数据移至“目的进程号”
	
MPI_Cart_sub( 旧笛卡尔通信子, int*保留的维号, &新笛卡尔通信子 );	
	//？？？将旧通信子中的指定维抽出来，形成新的较低维通信子
	  “保留的维号”中为1的维保留，为0的维丢弃
```
	
### 6.2 无向图拓扑

```
MPI_Graph_create( 旧通信子, int 结点数, int*度数和列表, int*边列表, int 重排判断, &无向图通信子 );		
	//创建无向图拓扑结构。允许两个结点间有多条边
	  第i个“度数和列表”指的是0~i进程的度数和
	  第i个结点与{i_0,...,i_ni}这些结点相连，则数组{0_0,...,0_n0,1_0,...,1_n1,...}组成“边列表”数组
	//例：图 0-2-1：结点数：3。度数和列表：{1,2,4}。边列表：{2,2,0,1}
MPI_Graph_neighbors_count( 无向图通信子, int 秩, int*邻居数 );
	//返回该秩的邻居数
MPI_Graph_neighbors( 无向图通信子, int 秩, int 邻居数, int*邻居列表 );
	//返回该秩的邻居列表
```

	
## 7 其他

### 7.1 编译与运行

OpenMPI:
```
mpicc mpi_hello.c -o mpi_hello.out 	
mpiexec -n 4 ./mpi_hello.out
```
IntelMPI：
```
mpiicpc mpi_hello.c -o mpi_hello.out 
mpirun -n 4 ./mpi_hello.out
```
windows下MS_MPI的用法：    
（环境变量 C_INCLUDEDE_PATH、CPLUS_INCLUDE_PATH、LIBRARY_PATH、Path）    
```	
g++ mpi_hello.cpp -o mpi_hello.exe -lmsmpi
mpiexec -n 4 mpi_hello.exe
```

### 7.2 I/O处理	
		
- 所有进程都能输出stdout    
- 仅0号进程能读入stdin

### 7.3 空对象

```
MPI_COMM_NULL      	空通信器
MPI_OP_NULL        	空操作
MPI_GROUP_NULL     	空进程组
MPI_DATATYPE_NULL  	空数据类型
MPI_REQUEST_NULL   	空请求(空回执)
MPI_ERRHANDLER_NULL	空错误处理过程
```