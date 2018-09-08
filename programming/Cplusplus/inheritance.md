# C++ 继承 笔记

## 类

```c++
class Base
{
	virtual void 虚成员函数甲();		//虚函数。通过基类指针/引用调用虚函数时，动态绑定，即运行时才确定调用哪个虚函数
	virtual void 纯虚成员函数()=0;		//纯虚函数。禁止用户创建Base对象（即b）	
	virtual void 禁重载成员函数() final;		//禁止派生类对该成员函数重载
}b;

class Derived: public Base
{
	virtual void 虚成员函数甲();		//基类与派生类虚函数声明虚完全匹配
										//例外：基类为 Base & 虚成员函数甲();
										//		派生类可为 Base & 虚函数成员甲(); 或 Derived & 虚函数成员甲();
										//		指针同理
	void 必为重载成员函数() override;		//该成员函数必须是其基类同名函数的重载
}d;
//类外部定义成员函数时，不能重复写virtual字，final字，override字
```
禁止别的函数继承自己：
```c++
class No_Base final{};
```

强制函数调用虚函数特定版本：
```c++
Base *pb = &d;	pb->Base::虚成员函数甲();
Base &yb = d;	yb.Base::虚成员函数甲();
```

基类重载函数后，派生类若要重定义，需重定义重载函数的所有版本：
```c++
class Derived
{
	using Base::f;	//继承所有重载版本，只重定义重载函数的特定版本即可
	using Base::Base;
};
```



## 成员权限	

```c++
class Base
{
	void 成员函数( Base ib)
	{
		pub_b;	//可
		pro_b;	//可
		pri_b;	//可
		ib.pub_b;	//可
		ib.pro_b;	//可
		ib.pri_b;	//可
	}
	public: int pub_b;
	protected: int pro_b;
	private: int pri_b;		
};

其他函数( Base ib)
{
	ib.pub_b;	//可
	ib.pro_b;	//否
	ib.pri_b;	//否
}

class Derived: public Base
{
	void 成员函数( Base ib, Derived id)
	{
		ib.pub_b;	//可
		ib.pro_b;	//否
		ib.pri_b;	//否
		pub_b;	//可
		pro_b;	//可
		pri_b;	//否
		id.pub_b;	//可
		id.pro_b;	//可
		id.pri_b;	//否
	}
};
```

派生类可以进一步限制但不能放松对所继承的成员的访问。	
Derived中的变量如下：

| 				 | pub_b	 | pro_b	 | pri_b |
| - | - | - | - |
| public Base	 | public	 | protected | 否    |
| protected Base | protected | protected | 否    |
| private Base	 | private	 | private	 | 否    |

恢复继承成员访问级别：
```c++
class Derived: private Base
{ 	public: using Base::pro_b; };	

Base 				//只保留属于Base的部分成员
	*pb = &d,		//对
	&yb = d;		//对
Derived 
	*pd = &b,		//错
	&yd = b;		//错
```



## 特定函数

构造函数不可为虚函数：
```c++
Derived::Derived( int pub_ib, int pro_ib, int pri_ib, int pri_id)
	:Base( pub_ib, pro_ib, pri_ib),
	 pri_d( pri_id){}
Derived::Derived( const Derived &id)
	:Base( id),
	 pri_d( id.pri_d){}
```

析构函数必须为虚函数：
```c++
Derived::~Derived()
{ 清除自己成员即可，编译器会自动调用 ~Base(); }		
```

static成员只有一个。
static成员函数不可为虚函数。



## 多重继承

```c++
class Derived_mul: public Base1, private Base2 {};
```

### 虚基类
派生类Derived_mul中只出现Basebase的一个副本，避免重复继承：
```c++
class Base1: virtual public Basebase{};
class Base2: public virtual Basebase{};
```

### 构造函数
非虚基类不能初始化简介基类，但虚基类需要：
```c++
Derived_mul::Derived_mul(形参表)
	:Basebase(实参), Base1(实参), Base2(实参), prid_(实参){}
```



## 其他

前向声明：
```c++
class Base;
class Derived;
```

友元关系不能继承。
