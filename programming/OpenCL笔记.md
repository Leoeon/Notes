#OpenCL�ʼ�
Leoeon    
2014.06.02


	cl_int ����״̬;
	�������ú�Ӧ����Ƿ� ����״̬==CL_SUCCESS

### 1.���������豸����

	cl_uint ƽ̨��Ŀ;
	cl_int ����״̬ = clGetPlatformIDs( 0, NULL, cl_uint* (&ƽ̨��Ŀ));
	cl_platform_id *ƽ̨�б� = ��cl_platform_id*) malloc(ƽ̨��Ŀ*sizeof(cl_platform_id));
	cl_int ����״̬ = clGetPlatformIDs( cl_uint ƽ̨��Ŀ, cl_platform_id* ƽ̨�б�, NULL);
		//��ȡָ��ϵͳ�Ͽ��õļ���ƽ̨
		
	cl_uint �豸��Ŀ;
	cl_int ����״̬ = clGetDeviceIDs( cl_platform_id ƽ̨�б�[0], cl_device_type �豸����, 0, NULL, cl_uint* (&�豸��Ŀ))
	cl_device_id *�豸�б� = (cl_device_id*)malloc(�豸��Ŀ*sizeof(cl_device_id));
	cl_int ����״̬ = clGetDeviceIDs( cl_platform_id ƽ̨�б�[0], cl_device_type �豸����, cl_uint �豸��Ŀ, cl_device_id* �豸�б�, NULL);
		//��ȡָ��ƽ̨�Ͽ��õļ����豸
		//�豸���ͣ�
			CL_DEVICE_TYPE_GPU����GPU
			CL_DEVICE_TYPE_CPU����CPU
			CL_DEVICE_TYPE_ACCELERATOR��OpenGL ר�ü�����
			CL_DEVICE_TYPE_DEFAULT��ϵͳĬ��OpenCL�豸
			CL_DEVICE_TYPE_ALL�������豸
		
### 2.ִ�л���

	cl_context ������ = clCreateContext( const cl_context_properties* ����������, cl_uint �豸��Ŀ, const cl_device_id* �豸�б�, void(*�ص�����), void* �ص�����ʵ��, cl_int* (&����״̬));
		//�½������ģ�����Э�����������豸֮��Ľ������ƣ�������������С��ڴ桢������ں˵ȶ��󣬲�����������ָ����һ�������豸��ִ���ں�
	//��
	cl_context ������ = clCreateContextFromType ( cl_context_properties* ����������, cl_device_type �豸����,void(*�ص�����), void* �ص�����ʵ��, cl_int* (&����״̬));
		//�����豸���ʹ���һ��OpenCL�����ģ����豸����������ʶҪʹ�õ��豸
		//����������ָ��һ���б��������������������Ƽ����Ӧ��ֵ��ÿ���������ƺ���������Ӧ������ֵ
		//void(*�ص�����)( const char* errinfo, const void* private_info, size_t cb, void* �ص������β�);
	
	cl_command_queue ������� = clCreateCommandQueue( cl_context ������, cl_device_id �豸�б�[0], cl_command_queue_properties ��������, cl_int* (&����״̬));
		//�½�������в��������豸
		//ÿ���������ֻ����һ���豸
		//�������ԣ�
			CL_QUEUE_PROFILING_ENABLE����������
			CL_QUEUE_OUT_OF_ORDER_EXEC_MODE_ENABLE������ִ����������û�ָ��������ϵ��ȷ����ȷ��ִ��˳��
	cl_int clSetCommandQueueProperty ( cl_command_queue �������, cl_command_queue_properties ��������, cl_bool �ܷ�, cl_command_queue_properties* ֮ǰ������);
		//ʹ�ܻ��ֹ������е�����
		//�ܷ�
			CL_TRUE������
			CL_FALSE����ֹ

### 3.�������
			
	//��������ʽ��д�ڴ�
	cl_mem �ڴ�ռ� = clCreateBuffer( cl_context ������, cl_mem_flags ��д״̬, size_t �ڴ��С, void* host_ptr, cl_int* (&����״̬));
		//Ϊ�豸�����ڴ滺����
	cl_int ����״̬ = clEnqueueWriteBuffer( cl_command_queue �������, cl_mem �ڴ�ռ�, cl_bool ����״̬, size_t ƫ����, size_t �ڴ��С, const void* д������ָ��, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_event* event);
		//�����������ݴ����豸�ڴ���
	cl_int ����״̬ = clEnqueueReadBuffer( cl_command_queue �������, cl_mem �ڴ�ռ�, cl_bool ����״̬, size_t ƫ����, size_t �ڴ��С, const void* ��������ָ��, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_event* event);
		//���豸�ڴ������ݴ���������
	cl_int clEnqueueCopyBuffer (cl_command_queue �������, cl_mem ���ڴ�ռ�, cl_mem ���ڴ�ռ�, size_t ��ƫ����, size_t ��ƫ����, size_t �ڴ��С, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//�����ڴ�ռ俽�������ڴ�ռ���
			
	//��imag��ʽ��д�ڴ�
	cl_mem clCreateImage2D( cl_context ������, cl_mem_flags ��д״̬, const cl_image_format* ͼ���ʽ������, size_t ͼ���, size_t ͼ���, size_t һά����Ƭ�ֽ���, void* Ӧ���ѷ���ͼ������, cl_int* (&����״̬));
	cl_mem clCreateImage3D( cl_context ������, cl_mem_flags ��д״̬, const cl_image_format* ͼ���ʽ������, size_t ͼ���, size_t ͼ���, size_t ͼ����, size_t һά����Ƭ�ֽ���, size_t ��ά�����Ƭ���ֽ���, void* Ӧ���ѷ���ͼ������, cl_int* (&����״̬));
		//����ͼ�����
	typedef struct _cl_image_format {
		cl_channel_order ͨ����Ŀ�Ͳ���;
		cl_channel_type ͨ���������ʹ�С;}	
	cl_int clEnqueueWriteImage( cl_command_queue �������, cl_mem ͼ��ռ�, cl_bool ����״̬, const size_t ƫ����[3], const size_t ��ά�߳�[3], size_t row_pitch, size_t slice_pitch, void* �����ڴ�, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//�����������ݴ����豸�ڴ���
	cl_int clEnqueueReadImage( cl_command_queue �������, cl_mem ͼ��ռ�, cl_bool ����״̬, const size_t ƫ����[3], const size_t ��ά�߳�[3], size_t row_pitch, size_t slice_pitch, void* �����ڴ�, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//���豸�ڴ������ݴ���������
	cl_int clEnqueueCopyImage (cl_command_queue �������, cl_mem ��ͼ��ռ�, cl_mem ��ͼ��ռ�, const size_t ��ƫ����[3], const size_t ��ƫ����[3], const size_t ��ά�߳�[3], cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//����ͼ��ռ俽������ͼ��ռ���
		
	cl_int clEnqueueCopyImageToBuffer( cl_command_queue �������, cl_mem ��ͼ��ռ�, cl_mem ���ڴ�ռ�, const size_t ��ƫ����[3], const size_t ����ά�߳�[3], size_t ��ƫ����, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//��ͼ��ռ俽�����ڴ�ռ���
	cl_int clEnqueueCopyBufferToImage( cl_command_queue �������, cl_mem ���ڴ�ռ�, cl_mem ��ͼ��ռ�, size_t ��ƫ����, const size_t ��ƫ����[3], const size_t ����ά�߳�[3], cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//���ڴ�ռ俽����ͼ��ռ���	
		
	void* ������ַ = clEnqueueMapBuffer( cl_command_queue �������, cl_mem �ڴ�ռ�, cl_bool ����״̬, cl_map_flags MAP��д״̬, size_t ƫ����, size_t �ڴ��С, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event, cl_int* (&����״̬));
		//���ڴ�ռ��ĳ������ӳ�䵽������ַ�ռ��У�������һ��ָ��ӳ�������ָ��
	void* ������ַ = clEnqueueMapImage( cl_command_queue �������, cl_mem ͼ��ռ�, cl_bool ����״̬, cl_map_flags MAP��д״̬, const size_t ƫ����[3], const size_t ��ά�߳�[3], size_t*(&һά����Ƭ�ֽ���), size_t*(&��ά�����Ƭ���ֽ���), cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event, cl_int *errcode_ret);
		//��ͼ��ռ��ĳ������ӳ�䵽������ַ�ռ��У�������һ��ָ��ӳ�������ָ��
	cl_int clEnqueueUnmapMemObject (cl_command_queue �������, cl_mem �ڴ�/ͼ��ռ�, void* ������ַ, cl_uint num_events_in_wait_list, const cl_event *event_wait_list, cl_event *event);
		//��֮ǰ��ӳ����ڴ����������н�ӳ��		

		//��д״̬��
			CL_MEM_READ_ONLY��ֻ��
			CL_MEM_WRITE_ONLY��ֻд
			CL_MEM_READ_WRITE����д
			CL_MEM_USE_HOST_PTR��
			CL_MEM_ALLOC_HOST_PTR��
			CL_MEM_COPY_HOST_PTR����host_ptr����������
				����ڴ�����������ͼ�����ʱ��mem_flags��������CL_MEM_USE_HOST_PTR���ͻ᣺
				��clEnqueueMapBuffer��clEnqueueMapImage���������ʱ����֤clCreateBuffer��clCreateImage{2D|3D}�е�host_ptr������ӳ��������������ݡ�			
		//MAP��д״̬��CL_MAP_READ �� CL_MAP_WRITE
		//�ڴ��С ���ֽ�Ϊ��λ
		//ͼ���ͼ��ߵ�λΪ����
		//��дƫ���� �� ��ά�߳� ������Ϊ��λ	
		//����״̬��
			CL_TRUE������clEnqueueWriteBuffer��������ֱ��д�������
			CL_FALSE������clEnqueueWriteBuffer������������д�������
		//event_wait_list��num_events_in_wait_listָ����ִ�д�����ǰ������ɵ��¼���event����һ���¼�����������ʶ�˶�д���Ҳ����������ѯ��ȴ�����������
		
		
### 4.�ڴ�ģ��
	
	ȫ���ڴ棺__global���豸�����м��㵥λ����
	�����ڴ棺__constant���豸�����м��㵥λ����
	�����ڴ棺__local��workgroup����
	˽���ڴ棺__private��ֻ�Ե���work-item�ɼ�
	
	const char* �˴��� = {
	"__kernel							",
	"void �˺�����( __global �β�){}	"}
	//��
	const char* �˴����ļ�·�� = shrFindFilePath( "�˺����ļ���.cl", NULL);
	const char* �˴��� = oclLoadProgSource( �˴����ļ�·��, "", &�ļ�����);
	
	get_work_dim();					//�����̵߳��ȵ�ά��
	get_num_groups(�ڼ�ά);			//���ظ�ά��ȫ����work-group����Ŀ
	get_global_size(�ڼ�ά);		//���ظ�ά��ȫ����work-item����Ŀ
	get_local_size(�ڼ�ά);			//���ظ�ά��work-group��work-item����Ŀ
	get_group_id(�ڼ�ά);			//���ظ�ά�ϵ�ǰwork-group��ȫ���е�����
	get_global_id(�ڼ�ά);			//���ظ�ά�ϵ�ǰwork-item��ȫ���е�����
	get_local_id(�ڼ�ά);			//���ظ�ά�ϵ�ǰwork-item��work-group�е�����
	
### 5.ִ�к���

	cl_program �˴������ = clCreateProgramWithSource( cl_context ������, cl_uint �˴��봮����, (const char**)&�˴���, const size_t* �˴��봮����, cl_int* (&����״̬));
		//������ش���
	//��
	cl_program �˴������ = clCreateProgramWithBinary( cl_context ������, cl_uint �豸��Ŀ, const cl_device_id* �豸�б�, const size_t* �����ƴ��볤��, const unsigned char **ÿ���豸�ж����ƴ���, cl_int* �豸װ�ص���״̬�б�, cl_int* (&����״̬));
		//������ض����ƴ���
	
	cl_int ����״̬ = clBuildProgram( cl_program �˴������, cl_uint �豸��Ŀ, const cl_device_id* �豸�б�, const char* ����ѡ��, void (*�ص�����), void* �ص�����ʵ��);
		//�����豸�еĴ��루���н׶α��룩	
		//void (*�ص�����)(cl_program, void* �ص������β�)
	
	cl_kernel �˶��� = clCreateKernel( cl_program �˴������, const char* "�˺�����", cl_int* (&����״̬));
		//��ȡ������ڵ�
	//��
	cl_int clCreateKernelsInProgram( cl_program �˴������, cl_uint �˶�����Ŀ, cl_kernel* �˶����б�, cl_uint* �˴�������к˶�����Ŀ);
		//Ϊ program �����к˺��������˶���
	
	cl_int ����״̬ = clSetKernelArg( cl_kernel �˶���, �ڼ�������, ������С, ʵ��ָ��);
		//����ʵ��
	
	cl_int ����״̬ = clEnqueueNDRangeKernel( cl_command_queue �������, cl_kernel �˶���, cl_uint �½�work-item��ά��, const size_t* work-item��ȫ��ID, const size_t* NDRange��ÿάwork-item����, const size_t* workgroup��ÿάwork-item������, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_int* event);
		//�������豸��ִ�к˶���
	//��
	cl_int clEnqueueTask( cl_command_queue �������, cl_kernel �˶���, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_int* event);
		//�������豸��ִ�к˶��󡣺˶�����ִ��ʱ��ʹ�õ���������
	//��
	cl_int clEnqueueNativeKernel (cl_command_queue �������, void (*�ɱ��������õĺ���)(void *), void* ������������ʱ���õ�ʵ���б�,  size_t ����������������, cl_uint �������������ݵĻ���������Ŀ, const cl_mem* �������������ݵĻ������, const void** �������������洢�ڴ��������λ��, cl_uint num_events_in_wait_list, const cl_event* event_wait_list, cl_int* event);
		//ִ��һ��ԭ���ģ������� OpenCL ����������ģ�C/C++����
		//�豸�� CL_DEVICE_EXECUTON_CAPABILITIES �б��������� CL_EXEC_NATIVE_KERNEL
	
	cl_int ����״̬ = clFlush( cl_command_queue �������);
		//����ֱ�������������������Ƴ����У�δ��ִ�����
	cl_int ����״̬ = clFinish( cl_command_queue �������); 
		//��������������е������������
		
### 6.�鿴������Ϣ

	//��ѯ��ͨ���÷�ʾ����
	cl_int clGetProgramBuildInfo( cl_program �˴������, cl_device_id �豸�б�[0], cl_program_build_info ������, 0, NULL, size_t* (&��ѯ����ֵʵ���ֽ���));
	char* ��ѯ����ֵ = new char[��ѯ����ֵʵ���ֽ���+1];
	cl_int clGetProgramBuildInfo( cl_program �˴������, cl_device_id �豸�б�[0], cl_program_build_info ������, size_t ��ѯ����ֵʵ���ֽ���, void* ��ѯ����ֵ, NULL);
	��ѯ����ֵ[��ѯ����ֵʵ���ֽ���] = "\0";

	cl_int clGetPlatformInfo ( cl_platform_id ƽ̨�б�[0], cl_platform_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���); 
		//ȷ������ʹ����һ���Ѷ����ʵ��ƽ̨
		//��ѯ��Ϣ��
			CL_PLATFORM_PROFILE��ʵ����֧�ֵļ����ƣ���FULL_PROFILE��ʵ��֧�� OpenCL �淶����EMBEDDED_PROFILE��ʵ��֧�� OpenCLǶ��ʽ�򵵣�����
			CL_PLATFORM_VERSION��OpenCL�汾
			CL_PLATFORM_NAME��ƽ̨����
			CL_PLATFORM_VENDOR��ƽ̨��Ӧ��
			CL_PLATFORM_EXTENSIONS��ƽ̨��֧�ֵ���չ���ֵ��б�
	cl_int clGetDeviceInfo ( cl_device_id �豸�б�[0], cl_device_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//��ȡһ�� OpenCL �豸���ض���Ϣ
	
	cl_int clRetainContext (cl_context ������);
		//���������ĵ����ü���
	cl_int clReleaseContext ( cl_context ������);
		//��С�����ĵ����ü���
	cl_int clGetContextInfo (cl_context ������, cl_context_info param_name, cl_device_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//��ѯ�����������Ϣ
		//��ѯ��Ϣ��
			CL_CONTEXT_REFERENCE_COUNT�������ĵ����ü���
			CL_CONTEXT_DEVICES���������е��豸�б�
			CL_CONTEXT_PROPERTIES������������
			
	cl_int clRetainCommandQueue( cl_command_queue �������);
		//����������е����ü���
	cl_int clReleaseCommandQueue( cl_command_queue �������);
		//��С������е����ü���
	cl_int clGetCommandQueueInfo( cl_command_queue �������, cl_context_info param_name, cl_device_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//��ѯ������������Ϣ
		//��ѯ��Ϣ��
			CL_QUEUE_CONTEXT:�����������ʱ��ָ����������
			CL_QUEUE_DEVICE:�����������ʱ��ָ�����豸
			CL_QUEUE_REFERENCE_COUNT:������е����ü���
			CL_QUEUE_PROPERTIES:������еĶ�������

	cl_int clRetainMemObject( cl_mem �ڴ�ռ�);
		//�����ڴ�ռ�����ü���
	cl_int clReleaseMemObject( cl_mem �ڴ�ռ�);
		//��С�ڴ�ռ�����ü���
			
	cl_int clGetSupportedImageFormats( cl_context ������, cl_mem_flags ��д״̬, cl_mem_object_type ͼ������, cl_uint ͼ���ʽ֧���б��ڴ������Դ洢������ͼ���ʽ, cl_image_format* ͼ���ʽ֧���б�, cl_uint* ��֧��ͼ���ʽ��ʵ����Ŀ);
		//��ȡOpenCLʵ����֧�ֵ�ͼ���ʽ�б�
		//ͼ�����ͣ�CL_MEM_OBJECT_IMAGE2D �� CL_MEM_OBJECT_IMAGE3D
		
	cl_int clGetMemObjectInfo( cl_mem �ڴ�/ͼ��ռ�, cl_mem_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//��ȡ�ڴ�/ͼ��ռ乲�е���Ϣ
	cl_int clGetImageInfo (cl_mem ͼ��ռ�, cl_image_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//��ȡͼ��ռ����е���Ϣ

	cl_int clRetainProgram( cl_program �˴������);
		//����˴����������ü���
	cl_int clReleaseProgram( cl_program �˴������);
		//��С�˴����������ü���
	cl_int clGetProgramInfo( cl_program �˴������, cl_program_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//��ȡ����������Ϣ			  
	cl_int clGetProgramBuildInfo( cl_program �˴������, cl_device_id �豸�б�[0], cl_program_build_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//�鿴������Ϣ
		
	cl_int clRetainKernel( cl_kernel �˶���);
		//����˶�������ü���
	cl_int clReleaseKernel( cl_kernel �˶���);	
		//��С�˶�������ü���
	cl_int clGetKernelInfo (cl_kernel �˶���, cl_kernel_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//�鿴�˶�����Ϣ
	cl_int clGetKernelWorkGroupInfo (cl_kernel �˶���, cl_device_id �豸�б�[0], cl_kernel_work_group_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//�鿴ĳ�豸�Ϻ˶�����Ϣ	
		
	cl_ulong startTime;
	clGetEventProfilingInfo( ev, CL_PROFILING_COMMAND_START, sizeof(cl_ulong), &startTime, NULL);	

### 7.�ͷ�
	clReleaseKernel(�˶���);
	clReleaseProgram(�˴������);
	clUnloadCompiler (void);
	clReleaseCommandQueue(�������);
	clReleaseMemObject(�ڴ�ռ�);
	clReleaseContext(������);
	
### 8.������
		
	cl_sampler ������ = clCreateSampler( cl_context ������, cl_bool �Ƿ�淶��, cl_addressing_mode ����Խ��ͼ�����귽ʽ, cl_filter_mode ��ȡͼ��ʱ�����õ��˲�ģʽ, cl_int* (&����״̬))
		//����һ������������
		//�Ƿ�淶����CL_TRUE �� CL_FALSE
		//����Խ��ͼ�����귽ʽ��CL_ADDRESS_REPEAT��CL_ADDRESS_CLAMP_TO_EDGE��CL_ADDRESS_CLAMP��CL_ADDRESS_NONE
		//��ȡͼ��ʱ�����õ��˲�ģʽ��CL_FILTER_NEAREST �� CL_FILTER_LINEAR
	cl_int clRetainSampler (cl_sampler ������);
		//��������������ü���
	cl_int clReleaseSampler (cl_sampler ������);
		//��С�����������ü���
	cl_int clGetSamplerInfo (cl_sampler ������, cl_mem_info ��ѯ��Ϣ, size_t ��ѯ����ֵ�ֽ���, void* ��ѯ����ֵ, size_t* ��ѯ����ֵʵ���ֽ���);
		//��ȡ�����������Ϣ