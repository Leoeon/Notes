#OpenCL笔记
Leoeon    
2014.06.02


	cl_int 调用状态;
	函数调用后都应检查是否 调用状态==CL_SUCCESS

### 1.主机——设备交互

	cl_uint 平台数目;
	cl_int 调用状态 = clGetPlatformIDs( 0, NULL, cl_uint* (&平台数目));
	cl_platform_id *平台列表 = （cl_platform_id*) malloc(平台数目*sizeof(cl_platform_id));
	cl_int 调用状态 = clGetPlatformIDs( cl_uint 平台数目, cl_platform_id* 平台列表, NULL);
		//获取指定系统上可用的计算平台
		
	cl_uint 设备数目;
	cl_int 调用状态 = clGetDeviceIDs( cl_platform_id 平台列表[0], cl_device_type 设备类型, 0, NULL, cl_uint* (&设备数目))
	cl_device_id *设备列表 = (cl_device_id*)malloc(设备数目*sizeof(cl_device_id));
	cl_int 调用状态 = clGetDeviceIDs( cl_platform_id 平台列表[0], cl_device_type 设备类型, cl_uint 设备数目, cl_device_id* 设备列表, NULL);
		//获取指定平台上可用的计算设备
		//设备类型：
			CL_DEVICE_TYPE_GPU：仅GPU
			CL_DEVICE_TYPE_CPU：仅CPU
			CL_DEVICE_TYPE_ACCELERATOR：OpenGL 专用加速器
			CL_DEVICE_TYPE_DEFAULT：系统默认OpenCL设备
			CL_DEVICE_TYPE_ALL：所有设备
		
### 2.执行环境

	cl_context 上下文 = clCreateContext( const cl_context_properties* 上下文属性, cl_uint 设备数目, const cl_device_id* 设备列表, void(*回调函数), void* 回调函数实参, cl_int* (&调用状态));
		//新建上下文，用于协调主机——设备之间的交互机制，即管理命令队列、内存、程序和内核等对象，并在上下文所指定的一个或多个设备上执行内核
	//或
	cl_context 上下文 = clCreateContextFromType ( cl_context_properties* 上下文属性, cl_device_type 设备类型,void(*回调函数), void* 回调函数实参, cl_int* (&调用状态));
		//根据设备类型创建一个OpenCL上下文，此设备类型用来标识要使用的设备
		//上下文属性指向一个列表，其中有上下文属性名称及其对应的值。每个属性名称后面紧跟其对应的期望值
		//void(*回调函数)( const char* errinfo, const void* private_info, size_t cb, void* 回调函数形参);
	
	cl_command_queue 命令队列 = clCreateCommandQueue( cl_context 上下文, cl_device_id 设备列表[0], cl_command_queue_properties 队列属性, cl_int* (&调用状态));
		//新建命令队列并关联到设备
		//每个命令队列只关联一个设备
		//队列属性：
			CL_QUEUE_PROFILING_ENABLE：程序剖析
			CL_QUEUE_OUT_OF_ORDER_EXEC_MODE_ENABLE：乱序执行命令。（由用户指定依赖关系以确保正确的执行顺序）
	cl_int clSetCommandQueueProperty ( cl_command_queue 命令队列, cl_command_queue_properties 队列属性, cl_bool 能否, cl_command_queue_properties* 之前的属性);
		//使能或禁止命令队列的属性
		//能否：
			CL_TRUE：允许
			CL_FALSE：禁止

### 3.缓冲对象
			
	//以数组形式读写内存
	cl_mem 内存空间 = clCreateBuffer( cl_context 上下文, cl_mem_flags 读写状态, size_t 内存大小, void* host_ptr, cl_int* (&调用状态));
		//为设备分配内存缓冲区
	cl_int 调用状态 = clEnqueueWriteBuffer( cl_command_queue 命令队列, cl_mem 内存空间, cl_bool 阻塞状态, size_t 偏移量, size_t 内存大小, const void* 写数组名指针, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_event* event);
		//将主机端数据传入设备内存中
	cl_int 调用状态 = clEnqueueReadBuffer( cl_command_queue 命令队列, cl_mem 内存空间, cl_bool 阻塞状态, size_t 偏移量, size_t 内存大小, const void* 读数组名指针, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_event* event);
		//将设备内存中数据传回主机端
	cl_int clEnqueueCopyBuffer (cl_command_queue 命令队列, cl_mem 旧内存空间, cl_mem 新内存空间, size_t 旧偏移量, size_t 新偏移量, size_t 内存大小, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//将旧内存空间拷贝到新内存空间中
			
	//以imag形式读写内存
	cl_mem clCreateImage2D( cl_context 上下文, cl_mem_flags 读写状态, const cl_image_format* 图像格式描述符, size_t 图像宽, size_t 图像高, size_t 一维宽切片字节数, void* 应用已分配图像数据, cl_int* (&调用状态));
	cl_mem clCreateImage3D( cl_context 上下文, cl_mem_flags 读写状态, const cl_image_format* 图像格式描述符, size_t 图像宽, size_t 图像高, size_t 图像深, size_t 一维宽切片字节数, size_t 二维宽高切片的字节数, void* 应用已分配图像数据, cl_int* (&调用状态));
		//创建图像对象
	typedef struct _cl_image_format {
		cl_channel_order 通道数目和布局;
		cl_channel_type 通道数据类型大小;}	
	cl_int clEnqueueWriteImage( cl_command_queue 命令队列, cl_mem 图像空间, cl_bool 阻塞状态, const size_t 偏移量[3], const size_t 三维边长[3], size_t row_pitch, size_t slice_pitch, void* 主机内存, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//将主机端数据传入设备内存中
	cl_int clEnqueueReadImage( cl_command_queue 命令队列, cl_mem 图像空间, cl_bool 阻塞状态, const size_t 偏移量[3], const size_t 三维边长[3], size_t row_pitch, size_t slice_pitch, void* 主机内存, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//将设备内存中数据传回主机端
	cl_int clEnqueueCopyImage (cl_command_queue 命令队列, cl_mem 旧图像空间, cl_mem 新图像空间, const size_t 旧偏移量[3], const size_t 新偏移量[3], const size_t 三维边长[3], cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//将旧图像空间拷贝到新图像空间中
		
	cl_int clEnqueueCopyImageToBuffer( cl_command_queue 命令队列, cl_mem 旧图像空间, cl_mem 新内存空间, const size_t 旧偏移量[3], const size_t 旧三维边长[3], size_t 新偏移量, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//将图像空间拷贝到内存空间中
	cl_int clEnqueueCopyBufferToImage( cl_command_queue 命令队列, cl_mem 旧内存空间, cl_mem 新图像空间, size_t 旧偏移量, const size_t 新偏移量[3], const size_t 新三维边长[3], cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//将内存空间拷贝到图像空间中	
		
	void* 主机地址 = clEnqueueMapBuffer( cl_command_queue 命令队列, cl_mem 内存空间, cl_bool 阻塞状态, cl_map_flags MAP读写状态, size_t 偏移量, size_t 内存大小, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event, cl_int* (&调用状态));
		//将内存空间的某个区域映射到主机地址空间中，并返回一个指向映射区域的指针
	void* 主机地址 = clEnqueueMapImage( cl_command_queue 命令队列, cl_mem 图像空间, cl_bool 阻塞状态, cl_map_flags MAP读写状态, const size_t 偏移量[3], const size_t 三维边长[3], size_t*(&一维宽切片字节数), size_t*(&二维宽高切片的字节数), cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event, cl_int *errcode_ret);
		//将图像空间的某个区域映射到主机地址空间中，并返回一个指向映射区域的指针
	cl_int clEnqueueUnmapMemObject (cl_command_queue 命令队列, cl_mem 内存/图像空间, void* 主机地址, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//对之前所映射的内存对象区域进行解映射		

		//读写状态：
			CL_MEM_READ_ONLY：只读
			CL_MEM_WRITE_ONLY：只写
			CL_MEM_READ_WRITE：读写
			CL_MEM_USE_HOST_PTR：
			CL_MEM_ALLOC_HOST_PTR：
			CL_MEM_COPY_HOST_PTR：从host_ptr处拷贝数据
				如果在创建缓冲对象或图像对象时，mem_flags中设置了CL_MEM_USE_HOST_PTR，就会：
				当clEnqueueMapBuffer或clEnqueueMapImage的命令完成时，保证clCreateBuffer或clCreateImage{2D|3D}中的host_ptr含有所映射区域的最新内容。			
		//MAP读写状态：CL_MAP_READ 或 CL_MAP_WRITE
		//内存大小 以字节为单位
		//图像宽、图像高单位为像素
		//读写偏移量 和 三维边长 以像素为单位	
		//阻塞状态：
			CL_TRUE：阻塞clEnqueueWriteBuffer函数返回直到写操作完成
			CL_FALSE：允许clEnqueueWriteBuffer函数返回先于写操作完成
		//event_wait_list和num_events_in_wait_list指定在执行此命令前必须完成的事件，event返回一个事件对象用来标识此读写命令，也可以用来查询或等待此命令的完成
		
		
### 4.内存模型
	
	全局内存：__global，设备中所有计算单位共享
	常量内存：__constant，设备中所有计算单位共享
	本地内存：__local，workgroup共享
	私有内存：__private，只对单个work-item可见
	
	const char* 核代码 = {
	"__kernel							",
	"void 核函数名( __global 形参){}	"}
	//或
	const char* 核代码文件路径 = shrFindFilePath( "核函数文件名.cl", NULL);
	const char* 核代码 = oclLoadProgSource( 核代码文件路径, "", &文件长度);
	
	get_work_dim();					//返回线程调度的维数
	get_num_groups(第几维);			//返回该维上全局中work-group的数目
	get_global_size(第几维);		//返回该维上全局中work-item的数目
	get_local_size(第几维);			//返回该维上work-group中work-item的数目
	get_group_id(第几维);			//返回该维上当前work-group在全局中的索引
	get_global_id(第几维);			//返回该维上当前work-item在全局中的索引
	get_local_id(第几维);			//返回该维上当前work-item在work-group中的索引
	
### 5.执行函数

	cl_program 核代码对象 = clCreateProgramWithSource( cl_context 上下文, cl_uint 核代码串数量, (const char**)&核代码, const size_t* 核代码串长度, cl_int* (&调用状态));
		//生成相关代码
	//或
	cl_program 核代码对象 = clCreateProgramWithBinary( cl_context 上下文, cl_uint 设备数目, const cl_device_id* 设备列表, const size_t* 二进制代码长度, const unsigned char **每个设备中二进制代码, cl_int* 设备装载调用状态列表, cl_int* (&调用状态));
		//生成相关二进制代码
	
	cl_int 调用状态 = clBuildProgram( cl_program 核代码对象, cl_uint 设备数目, const cl_device_id* 设备列表, const char* 编译选项, void (*回调函数), void* 回调函数实参);
		//编译设备中的代码（运行阶段编译）	
		//void (*回调函数)(cl_program, void* 回调函数形参)
	
	cl_kernel 核对象 = clCreateKernel( cl_program 核代码对象, const char* "核函数名", cl_int* (&调用状态));
		//提取代码入口点
	//或
	cl_int clCreateKernelsInProgram( cl_program 核代码对象, cl_uint 核对象数目, cl_kernel* 核对象列表, cl_uint* 核代码对象中核对象数目);
		//为 program 中所有核函数创建核对象
	
	cl_int 调用状态 = clSetKernelArg( cl_kernel 核对象, 第几个参数, 参数大小, 实参指针);
		//传入实参
	
	cl_int 调用状态 = clEnqueueNDRangeKernel( cl_command_queue 命令队列, cl_kernel 核对象, cl_uint 新建work-item的维度, const size_t* work-item的全局ID, const size_t* NDRange中每维work-item数量, const size_t* workgroup中每维work-item的数量, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_int* event);
		//请求在设备上执行核对象
	//或
	cl_int clEnqueueTask( cl_command_queue 命令队列, cl_kernel 核对象, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_int* event);
		//请求在设备上执行核对象。核对象在执行时仅使用单个工作项
	//或
	cl_int clEnqueueNativeKernel (cl_command_queue 命令队列, void (*可被主机调用的函数)(void *), void* 调用主机函数时所用的实参列表,  size_t 主机函数参数个数, cl_uint 主机函数所传递的缓冲对象的数目, const cl_mem* 主机函数所传递的缓冲对象, const void** 主机函数参数存储内存对象句柄的位置, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_int* event);
		//执行一个原生的（不是用 OpenCL 编译器编译的）C/C++函数
		//设备的 CL_DEVICE_EXECUTON_CAPABILITIES 中必须设置了 CL_EXEC_NATIVE_KERNEL
	
	cl_int 调用状态 = clFlush( cl_command_queue 命令队列);
		//阻塞直到命令队列中所有命令被移出队列，未必执行完毕
	cl_int 调用状态 = clFinish( cl_command_queue 命令队列); 
		//阻塞至命令队列中的所有命令完成
		
### 6.查看过程信息

	//查询的通用用法示例：
	cl_int clGetProgramBuildInfo( cl_program 核代码对象, cl_device_id 设备列表[0], cl_program_build_info 参数名, 0, NULL, size_t* (&查询返回值实际字节数));
	char* 查询返回值 = new char[查询返回值实际字节数+1];
	cl_int clGetProgramBuildInfo( cl_program 核代码对象, cl_device_id 设备列表[0], cl_program_build_info 参数名, size_t 查询返回值实际字节数, void* 查询返回值, NULL);
	查询返回值[查询返回值实际字节数] = "\0";

	cl_int clGetPlatformInfo ( cl_platform_id 平台列表[0], cl_platform_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数); 
		//确定具体使用哪一个已定义的实现平台
		//查询信息：
			CL_PLATFORM_PROFILE：实现所支持的简档名称，有FULL_PROFILE（实现支持 OpenCL 规范）和EMBEDDED_PROFILE（实现支持 OpenCL嵌入式简档）两种
			CL_PLATFORM_VERSION：OpenCL版本
			CL_PLATFORM_NAME：平台名字
			CL_PLATFORM_VENDOR：平台供应商
			CL_PLATFORM_EXTENSIONS：平台所支持的扩展名字的列表
	cl_int clGetDeviceInfo ( cl_device_id 设备列表[0], cl_device_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//获取一个 OpenCL 设备的特定信息
	
	cl_int clRetainContext (cl_context 上下文);
		//增大上下文的引用计数
	cl_int clReleaseContext ( cl_context 上下文);
		//减小上下文的引用计数
	cl_int clGetContextInfo (cl_context 上下文, cl_context_info param_name, cl_device_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//查询上下文相关信息
		//查询信息：
			CL_CONTEXT_REFERENCE_COUNT：上下文的引用计数
			CL_CONTEXT_DEVICES：上下文中的设备列表
			CL_CONTEXT_PROPERTIES：上下文属性
			
	cl_int clRetainCommandQueue( cl_command_queue 命令队列);
		//增大命令队列的引用计数
	cl_int clReleaseCommandQueue( cl_command_queue 命令队列);
		//减小命令队列的引用计数
	cl_int clGetCommandQueueInfo( cl_command_queue 命令队列, cl_context_info param_name, cl_device_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//查询命令队列相关信息
		//查询信息：
			CL_QUEUE_CONTEXT:创建命令队列时所指定的上下文
			CL_QUEUE_DEVICE:创建命令队列时所指定的设备
			CL_QUEUE_REFERENCE_COUNT:命令队列的引用计数
			CL_QUEUE_PROPERTIES:命令队列的队列属性

	cl_int clRetainMemObject( cl_mem 内存空间);
		//增大内存空间的引用计数
	cl_int clReleaseMemObject( cl_mem 内存空间);
		//减小内存空间的引用计数
			
	cl_int clGetSupportedImageFormats( cl_context 上下文, cl_mem_flags 读写状态, cl_mem_object_type 图像类型, cl_uint 图像格式支持列表内存最多可以存储多少条图像格式, cl_image_format* 图像格式支持列表, cl_uint* 所支持图像格式的实际数目);
		//获取OpenCL实现所支持的图像格式列表
		//图像类型：CL_MEM_OBJECT_IMAGE2D 或 CL_MEM_OBJECT_IMAGE3D
		
	cl_int clGetMemObjectInfo( cl_mem 内存/图像空间, cl_mem_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//获取内存/图像空间共有的信息
	cl_int clGetImageInfo (cl_mem 图像空间, cl_image_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//获取图像空间特有的信息

	cl_int clRetainProgram( cl_program 核代码对象);
		//增大核代码对象的引用计数
	cl_int clReleaseProgram( cl_program 核代码对象);
		//减小核代码对象的引用计数
	cl_int clGetProgramInfo( cl_program 核代码对象, cl_program_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//获取程序对象的信息			  
	cl_int clGetProgramBuildInfo( cl_program 核代码对象, cl_device_id 设备列表[0], cl_program_build_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//查看编译信息
		
	cl_int clRetainKernel( cl_kernel 核对象);
		//增大核对象的引用计数
	cl_int clReleaseKernel( cl_kernel 核对象);	
		//减小核对象的引用计数
	cl_int clGetKernelInfo (cl_kernel 核对象, cl_kernel_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//查看核对象信息
	cl_int clGetKernelWorkGroupInfo (cl_kernel 核对象, cl_device_id 设备列表[0], cl_kernel_work_group_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//查看某设备上核对象信息	
		
	cl_ulong startTime;
	clGetEventProfilingInfo( ev, CL_PROFILING_COMMAND_START, sizeof(cl_ulong), &startTime, NULL);	

### 7.释放
	clReleaseKernel(核对象);
	clReleaseProgram(核代码对象);
	clUnloadCompiler (void);
	clReleaseCommandQueue(命令队列);
	clReleaseMemObject(内存空间);
	clReleaseContext(上下文);
	
### 8.采样器
		
	cl_sampler 采样器 = clCreateSampler( cl_context 上下文, cl_bool 是否规范化, cl_addressing_mode 处理越界图像坐标方式, cl_filter_mode 读取图像时所采用的滤波模式, cl_int* (&调用状态))
		//创建一个采样器对象
		//是否规范化：CL_TRUE 或 CL_FALSE
		//处理越界图像坐标方式：CL_ADDRESS_REPEAT、CL_ADDRESS_CLAMP_TO_EDGE、CL_ADDRESS_CLAMP、CL_ADDRESS_NONE
		//读取图像时所采用的滤波模式：CL_FILTER_NEAREST 或 CL_FILTER_LINEAR
	cl_int clRetainSampler (cl_sampler 采样器);
		//增大采样器的引用计数
	cl_int clReleaseSampler (cl_sampler 采样器);
		//减小采样器的引用计数
	cl_int clGetSamplerInfo (cl_sampler 采样器, cl_mem_info 查询信息, size_t 查询返回值字节数, void* 查询返回值, size_t* 查询返回值实际字节数);
		//获取采样器相关信息