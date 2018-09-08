# fcntl 读写文件 笔记

```c++
#include <fcntl.h> 
```

```c++
int 文件标识 = open( const char *文件名, int 打开方式, mode_t 新建文件用户权限);
	//打开方式：
	//	O_RDONLY：只读
	//	O_WRONL：只写
	//	O_RDWR：可读写
	//	O_CREAT：若无原文件则建立该文件
	//	O_EXCL：配套O_CREAT，若无原文件则建立该文件，若有原文件则打开错误
	//	O_TRUNC：清空原文件
	//	O_APPEND：从原文件末尾开始读写
	//	O_NONBLOCK：不可阻断的方式打开文件，也就是无论有无数据读取或等待，都会立即返回进程之中
	//	O_NOCTTY：路径名指向终端设备，不要把这个设备用作控制终端
	//	O_SYNC：对该文件的写操作会等到数据被写到磁盘上才算结束 
	//	O_DIRECT：提供对直接I/O的支持 
	//	O_LARGEFILE：提供对超过 2GB 大文件的支持
	//	O_DIRECTORY：所打开的文件必须是目录
	//返回0则打开成功
		
int close(int 文件标识);
```

```c++
ssize_t read( int 文件标识, void *内存指针, size_t 字节数);
	//把文件读一定字节到指针所指的内存中
	//返回值为实际读到的字节数，若返回-1则错误
	
ssize_t write( int 文件标识, void *内存指针, size_t 字节数);
	//把指针所指的内存写一定字节到文件中
	//返回值为实际写入的字节数，若返回-1则错误	
```

```c++
struct stat
{
	dev_t st_dev;				// 文件的设备编号
	ino_t st_ino;				// 文件的i-node
	mode_t st_mode;				// 文件的类型和存取的权限
	nlink_t st_nlink;			// 连到该文件的硬连接数目，刚建立的文件值为1
	uid_t st_uid;				// 文件所有者的用户识别码
	gid_t st_gid;				// 文件所有者的组识别码
	dev_t st_rdev;				// 若此文件为装置设备文件，则为其设备编号
	off_t st_size;				// 文件大小，以字节计算
	unsigned long  st_blksize;	// 文件系统的I/O缓冲区大小
	unsigned long  st_blocks;	// 占用文件区块的个数，每一区块大小为512个字节
	time_t st_atime;			// 文件最近一次被存取或被执行的时间，一般只有在用mknod、utime、read、write与tructate时改变
	time_t st_mtime;			// 文件最后一次被修改的时间，一般只有在用mknod、utime和write时才会改变
	time_t st_ctime;			// i-node最近一次被更改的时间，此参数会在文件所有者、组、权限被更改时更新先前所描述的
};

int stat( const char *文件名, struct stat *buf);
	//取得文件状态
	//返回0则调用成功，返回-1则错误	

int fstat( int 文件标识, struct stat *文件状态);
	//除输入参数外，与stat完全相同
```
