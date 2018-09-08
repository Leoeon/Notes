# boost 序列化 笔记

## 基本用法
```c++
#include <boost/archive/text_oarchive.hpp>
#include <boost/archive/binary_oarchive.hpp>

std::ofstream ofs("文件名");
boost::archive::text_oarchive oar(ofs);
oar << 对象;

std::ifstream ifs("文件名");
boost::archive::text_iarchive iar(ifs);
iar >> 对象;
```

```c++
#include <boost/mpi.hpp>

boost::mpi::packed_oarchive oar(通信子);
boost::mpi::packed_iarchive iar(通信子);

ar.address()			// 作为MPI传输的“消息指针”
oar.size()				// 返回数据量
iar.resize(数据量);		// 设置内存数据量
MPI_PACKED				// 消息数据类型

oar << 对象;
iar >> 对象;
```

## 类

``` c++
#include <boost/serialization/vector.hpp>
```
``` c++
侵入式：
class 类名
{
	friend class boost::serialization::access;
	template<class Archive> void serialize(Archive & ar, const unsigned int version)
    {
		ar & boost::serialization::base_object<基类>(*this);
		ar & 对象.成员变量壹;
		ar & 对象.成员变量贰;
    }	
};

非侵入式：
class 类名
{
	template<typename Archive> friend void serialize(Archive&, 类名&, const unsigned int);
};
namespace boost
{
	namespace serialization
	{
		template<class Archive> void serialize(Archive & ar, 类名 & 对象, const unsigned int version)
		{
			ar & 对象.成员变量壹;
			ar & 对象.成员变量贰;
		}
	}
}

侵入式：
class 类名
{
	friend class boost::serialization::access;
    template<class Archive> void save(Archive & ar, const unsigned int version) const
    {
		ar & 对象.成员变量壹;
		ar & 对象.成员变量贰;
    }
    template<class Archive> void load(Archive & ar, const unsigned int version)
    {
        if(version > 0)
            ar & 对象.成员变量壹;
        ar & 对象.成员变量贰;
    }
    BOOST_SERIALIZATION_SPLIT_MEMBER();
};
```
