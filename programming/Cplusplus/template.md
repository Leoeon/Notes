# C++ 模板 笔记

## 模板函数

```c++
template< typename T, int N>
void 模板函数甲( const T(&形参壹)[N], const T &形参贰)
{
	typename T::size_type *局域变量;			//使用类型形参定义的类型成员
}
```

### 特化某函数			
以下特化皆可只特化部分模板形参
```c++
template<> void 模板函数甲( const int(&形参壹)[8], const int &形参贰){}
//或	
template<> void 模板函数甲<int,8>( const int(&形参壹)[8], const int &形参贰){}
```

### 实例化
```c++
int x[10],i;
模板函数甲(x,i);	
//或
模板函数甲<int,10>(x,i);
void(*模板函数指针甲)(const int (&)[10], const int &) = 模板函数甲;		//需要能从函数指针类型确定模板形参
```

### 变长模板
```c++
auto Sum() { return 0; }		//边界条件
template <typename T_head, typename... T_tail> auto Sum( T_head t_head, T_tail... t_tail)
{ return t_head + Sum(t_tail...); }		//递归的偏特化定义
Sum(2.0,3,5);	或	Sum<double,int,int>(2.0,3,5);	//实例化调用
```

### 文件
```c++
// 文件 file.h 中：
template< typename T, int N> void 模板函数甲( const T(&形参壹)[N], const T &形参贰);
// 文件 file-template.h 中：
template< typename T, int N> void 模板函数甲( const T(&形参壹)[N], const T &形参贰){}
extern template void 模板函数甲<int,8>( const int(&形参壹)[8], const int &形参贰);
// 文件 file.cpp 中：
template<> void 模板函数甲<int,8>( const int(&形参壹)[8], const int &形参贰);
```



## 模板类

```c++
template<typename T, int N>
class 模板类甲
{
	inline void 成员函数壹(T 形参壹) const;
	template<typename T2> void 成员函数贰(T2 形参贰);		//成员模板
	static int 静态数据成员叁;
};

template<typename T, int N> 
inline void 模板类甲<T,N>::成员函数壹(T 形参壹) const {}

template<typename T, int N> template<typename T2> 
void 模板类甲<T,N>::成员函数贰(T2 形参贰){}
```

### 特化某类
```c++
template<> class 模板类甲<double,20>{};

inline void 模板类甲<double,20>::成员函数壹(double 形参壹) {}
```

### 仅特化某类的某成员
```c++
template<> inline void 模板类甲<char,30>::成员函数壹(char 形参壹) {}
```

### 实例化
```c++
模板类甲<int,10> v;
template<typename T, int N> int 模板类甲<T,N>::静态数据成员叁 = 0;		//每个 模板的实例化 有一个自己的static成员
```

### 变长模板
```c++
template<typename... Ts, int... As>class 变长模板类乙{};

template<typename... Ts>class 变长模板类丙;	//变长模板的声明
template<typename T_head, typename... T_tails> class 变长模板类丙<T_head,T_tails...>:private 变长模板类丁<T_tails...>
{ 
	T_head t_head;
	变长模板类丙<T_head,T_tails...>(T_head t_head_in, T_tails... t_tails_in)
		:t_head(t_head_in),
		 变长模板类丙<T_tails...>(t_tails_in...){}
};	//递归的偏特化定义
template<>class 变长模板类丙<>{};	//边界条件

变长模板类丙<string,int,int> 变长模板类实例化丙("hi",4,5);	//实例化类
```
