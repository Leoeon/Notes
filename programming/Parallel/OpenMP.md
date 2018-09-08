# OpenMP 笔记

## omp

```c++
#pragma omp
指令：
	parallel							//开启多线程
	for
		//只能并行化有典型结构的for循环（for语句本身或循环前已确定循环次数，循环变量为有符号整型或指针，判定只能用<而非!=）
	reduction(运算:归约变量1,归约变量2)				//在线程合并后进行归约
		//运算(初始值)可为 +(0) *(1) -(0) &(~0) |(0) ^(0) &&(1) ||(0)
		//归约变量在线程内为私有变量，在线程外及归约时为共享变量
		//设(@:m)，m并行前为m0，操作符@对应初始值a0，则并行后m值为 m0@f1(a0)@f2(a0)@f3(a0)@……
	sections							//圈出可能会被并行执行的代码段
		//在sections代码段中，需要并行的语句前写 #pragma omp section，则这些语句与语句会并行
	critical(临界区名字)						//临界区
		//在编译时时设置，使相同名字的临界区同时只有一个线程运行
	barrier								//路障，阻塞所有线程直至所有线程到达该路障
	atomic								//高性能critical
		//只能保护一条语句，只保护自操作变量（即后式x），且仅限：四条x+=c、三条x&=c、两条x<<c、四条++x
	single								//只限某一个线程执行以下指令
	master								//只限主线程执行以下指令

子句：
	num_threads(设定线程数)						//指定线程数
		//不指定则默认为每一个内核开启一个线程
	private(变量1, 变量2)						//私有变量。未初始化
	shared(变量3)							//共享变量
	default(模式)
		//模式：
			none：所有变量必须显示说明私有或共享
			shared：传入并行区域内的同名变量被当作共享变量来处理，不会产生线程私有副本。除非使用private等子句
	firstprivate(变量1)						//每个线程都有它自己的变量私有副本，并且变量要被继承主线程中的初值
	lastprivate(变量2)						//将线程中的私有变量的值在并行处理结束后复制回主线程中的对应变量（程序语法顺序的最后一个赋值）
	threadprivate							//指定一个全局变量是线程私有的
	nowait								//解除某些指令的默认路障
		//parallel、for、single后有隐含路障
	schedule(分配方案, 参数)
		//仅用于for之后
		//分配方案：
			static：轮转分配“参数”个循环给每个线程
			dynamic：每个线程执行完毕后再申请“参数”个循环
			guided：每个线程执行完毕后再申请“剩余循环/线程数”个循环，降至“参数”个
			runtime：等价于	schedule($OMP_SCHEDULE)	具体取决于环境变量OMP_SCHEDULE的值
	ordered								//指定并行区域的循环按顺序执行	
	copyprivate							//在single指令中，将变量的值拷贝到各线程该量中
	copyin(变量1)							//将主线程中threadprivate变量的值拷贝到各线程该量中	
```



## 锁

### 简单锁						
只能上锁一次，然后解锁
```c++
omp_lock_t 锁；
omp_init_lock(&锁);		//初始化锁
omp_set_lock(&锁);		//上锁，阻塞直至上锁成功
omp_unset_lock(&锁);	//解锁
bool omp_test_lock(&锁);	//试图上锁，若立即成功则返回true，否则放弃且返回false
omp_destroy_lock(&锁);	//销毁锁
```
### 嵌套锁						
可被同一个线程上锁多次，然后解锁同样次数才算真正解锁



## 其他

```shell
g++ -fopenmp b.cpp
icc -qopenmp b.cpp
```

```c++
#ifdef _OPENMP
#	include<omp.h>
#endif
```
```c++
#ifdef _OPENMP	
	int 我的线程编号 = omp_get_thread_num();
	int 线程总数 = omp_get_num_threads();
	int 处理器总数 = omp_get_num_procs();
	omp_set_num_threads(设置并行执行代码时的线程个数);	
	int 可用线程数量 = omp_get_max_threads();
#else
	int 我的线程编号 = 0;
	int 线程总数 =1;
#endif
```
```shell
OMP_NUM_THREADS：并行线程数环境变量
```
```c++
omp_set_nested(true);		// 允许嵌套循环并行（默认不允许，只并行最外层循环）
```
