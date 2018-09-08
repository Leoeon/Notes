# C++11多线程

编译时需要 ```-std=c++11 -pthread ```


## thread

```c++
#include<thread>
```
```c++
void 函数名( int 形参甲, int &形参乙, const int &形参丙);
std::thread 线程( 函数名, 实参甲, std::ref(实参乙), std::cref(实参丙) );		//开新线程，开始执行 函数(实参甲,实参乙)。

void 类名::函数名( int 形参甲 );
std::thread 线程( &类名::函数名, &类对象, 实参甲 );
```
```c++
线程壹.join();			//主线程在此等子线程执行完毕，销毁线程。所有joinable的线程在主进程结束前都必须显示join()销毁掉。
线程贰.detach();		//子线程自生自灭。主进程结束时若还未完成则强制销毁

线程壹.joinable()		//检测线程是否为joinable的。被detach()的线程和空线程（没有分配到函数执行）不是joinable的
```
```c++
线程肆 = move(线程叁)							//线程不可复制，只能用这种形式转移线程变量
线程肆.swap(线程叁);	或	swap(线程肆,线程叁);	//交换两线程
线程.get_id()	或	std::this_thread::get_id()	//返回线程id
```
```c++
this_thread::get_id()
this_thread::sleep_for(时间范围);
thread::hardware_concurrency();		
```

## mutex
```c++
#include<mutex>
```
```c++
std::mutex 互斥锁;
std::recursive_mutex 多重互斥锁;			//同一线程可上多重锁，需解锁相同重数才算真正解锁
std::timed_mutex 互斥等待锁;
std::recursive_timed_mutex 多重互斥等待锁;
```
```c++
互斥锁.lock();								//上锁。若已被上锁则阻塞
互斥锁.try_lock();							//上锁。若已被上锁则返回false，继续执行代码
互斥等待锁.try_lock_for(时间范围);			//上锁。若已被上锁则阻塞等待一段时间：若时间范围内其他线程解锁则上锁成功；若超时则返回false，继续执行代码
互斥等待锁.try_lock_until(时间末);			//上锁。若已被上锁则阻塞等待至时间末：若时间范围内其他线程解锁则上锁成功；若超时则返回false，继续执行代码
互斥锁.unlock();							//解锁
```
```c++
std::lock_guard<std::mutex> 管理锁(互斥锁,上锁方式);
std::unique_lock<std::mutex> 高级管理锁(互斥锁,上锁方式);
// 上锁方式：
//    无                构造时调.lock()，析构时调.unlock()
//    std::adopt_lock   已知本线程已上锁，析构时调.unlock()
//    std::defer_lock   析构时调.unlock()
//    std::try_to_lock  构造时调.try_lock()，析构时调.unlock()
// 成员函数：
//     owns_lock() 或 重载bool值：返回是否已上锁
//     mutex()：返回其管理的互斥锁的指针
//     release()：返回其管理的互斥锁的指针，并释放所有权
//     lock()、try_lock()、try_lock_for()、try_lock_until()、unlock()
```

## atomic
```c++
#include<atomic>
```
```c++
// 原子布尔可视为锁
std::atomic_flag 原子布尔 = ATOMIC_FLAG_INIT;
原子布尔.test_and_set(内存顺序);							// 若未上锁则返回flase并上锁，若被锁则返回true并继续
原子布尔.clear(内存顺序);									// 解锁
```
```c++
atomic<T> 原子变量(初始化值);
原子变量.store(新值,内存顺序);                            <==>     原子变量 = 新值
旧值 = 原子变量.load(内存顺序);                           <==>     旧值 = 原子变量
旧值 = 原子变量.exchange(新值,内存顺序);                           
旧值 = 整形/指针原子变量.fetch_add(运算值,内存顺序);      <==>     新值 = (整形/指针原子变量+=运算值);
// 等价版本：内存顺序固定为 std::memory_order_seq_cst
```
