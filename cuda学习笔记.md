# cuda学习笔记

## 编译命令

```
nvcc -arch=sm_75 -o hello hello.cu -run
```

性能分析

```
nsys profile --stats=true ./hello
```



## hello world

```c++
#include <stdio.h>

void cpu(){
    printf("hello cpu \n");
}

__global__ void gpu(){
    printf("hello gpu\n");
}

int main(){
    cpu();
    gpu<<<1,1>>>();
    cudaDeviceSynchronize();
}
```

```
__global__  关键字表明以下函数将在GPU上运行并可全局调用，而在此情况下，则指由CPU或GPU调用
cpu代码为主机代码 host gpu代码为设备代码 device
__global__修饰函数的返回类型必须是void 
```

```
gpu<<<1,1>>>();
通常，当调用GPU上运行的函数时，我们将此函数称为已启动的核函数
启动核函数是，我们必须提供配置，即在向核函数传递任何预期参数之前使用<<<>>>语法完成配置
配置核函数启动指定线程层次结构
```

```
cudaDeviceSynchronize()
与许多c/c++代码不同，核函数启动方式为异步，cpu代码将继续执行而无需等待核函数完成启动
调用cuda运行时提供的cudaDeviceSynchronize()将导致主机(cpu)代码暂时等待，直至设备(gpu)代码完成，才能在cpu恢复运行
```

## 网格(grid)、块(block)、线程(thread)

grid 包含 block block包含thread

```c++
#include <stdio.h>

void cpu(int *a,int N){
    for (int i = 0; i < N; ++i)
    {
        a[i]=i;
    }
}

__global__ void gpu(int *a,int N){
    int i=blockIdx.x*blockDim.x+threadIdx.x;
    if (i<N)
    {
        a[i]*=2;
    }
    
}

bool checkout(int *a,int N){
    for (int i = 0; i < N; ++i)
    {
        if (a[i]!=2*i)
        {
            return false;
        }
    }
    return true;
}

int main(){
    int *a;
    const int N=100;
    size_t size=N*sizeof(int);

    cudaMallocManaged(&a,size);
    cpu(a,N);
    size_t threads=256;
    size_t blocks=(N+threads-1)/threads;
    gpu<<<blocks,threads>>>(a,N);
    cudaDeviceSynchronize();

    checkout(a,N)?printf("ok\n"):printf("error\n");

    cudaFree(a);

}
```

```
girdDim.x 表示网格中的块数(block)
blockIdx.x 表示当前块的索引
blockDim.x 表示块中的线程数(thread)
threadIdx.x 当前块中线程的索引

threadIdx是一个uint3类型，表示一个线程的索引。
blockIdx是一个uint3类型，表示一个线程块的索引，一个线程块中通常有多个线程。
blockDim是一个dim3类型，表示线程块的大小。
gridDim是一个dim3类型，表示网格的大小，一个网格中通常有多个线程块。
```

线程计算公式

```
一个grid可以包含多个block，block组织方式可以是一维、二维或者三维，block包含多个thread，这些thread的组织方式可以是一维、二维、或者三维
cuda中每一个线程有一个唯一的标识ID-ThreadIdx,这个ID随着grid和block的划分方式不同而变化。
```

```
1、 grid划分成1维，block划分为1维

    int threadId = blockIdx.x *blockDim.x + threadIdx.x;  
    
  
2、 grid划分成1维，block划分为2维  

    int threadId = blockIdx.x * blockDim.x * blockDim.y+ threadIdx.y * blockDim.x + threadIdx.x;  
  
  
3、 grid划分成1维，block划分为3维  

    int threadId = blockIdx.x * blockDim.x * blockDim.y * blockDim.z  
                       + threadIdx.z * blockDim.y * blockDim.x  
                       + threadIdx.y * blockDim.x + threadIdx.x;  

  
4、 grid划分成2维，block划分为1维  

    int blockId = blockIdx.y * gridDim.x + blockIdx.x;  
    int threadId = blockId * blockDim.x + threadIdx.x;  
   
  
5、 grid划分成2维，block划分为2维 

    int blockId = blockIdx.x + blockIdx.y * gridDim.x;  
    int threadId = blockId * (blockDim.x * blockDim.y)  
                       + (threadIdx.y * blockDim.x) + threadIdx.x;  
    
  
6、 grid划分成2维，block划分为3维

    int blockId = blockIdx.x + blockIdx.y * gridDim.x;  
    int threadId = blockId * (blockDim.x * blockDim.y * blockDim.z)  
                       + (threadIdx.z * (blockDim.x * blockDim.y))  
                       + (threadIdx.y * blockDim.x) + threadIdx.x;  
   
  
7、 grid划分成3维，block划分为1维 

    int blockId = blockIdx.x + blockIdx.y * gridDim.x  
                     + gridDim.x * gridDim.y * blockIdx.z;  
    int threadId = blockId * blockDim.x + threadIdx.x;  
   
  
8、 grid划分成3维，block划分为2维  

    int blockId = blockIdx.x + blockIdx.y * gridDim.x  
                     + gridDim.x * gridDim.y * blockIdx.z;  
    int threadId = blockId * (blockDim.x * blockDim.y)  
                       + (threadIdx.y * blockDim.x) + threadIdx.x;  
   
  
9、 grid划分成3维，block划分为3维

    int blockId = blockIdx.x + blockIdx.y * gridDim.x  
                     + gridDim.x * gridDim.y * blockIdx.z;  
    int threadId = blockId * (blockDim.x * blockDim.y * blockDim.z)  
                       + (threadIdx.z * (blockDim.x * blockDim.y))  
                       + (threadIdx.y * blockDim.x) + threadIdx.x
```

跨步循环

```c++
#include <stdio.h>

void cpu(int *a,int N){
    for (int i = 0; i < N; ++i)
    {
        a[i]=i;
    }
}

__global__ void gpu(int *a,int N){
    int threadi=blockIdx.x*blockDim.x+threadIdx.x;
    int sumthread=gridDim.x*blockDim.x;
    for (int i = threadi; i < N; i+=sumthread)
    {
        a[i]*=2;
    }
    
}

bool checkout(int *a,int N){
    for (int i = 0; i < N; ++i)
    {
        if (a[i]!=2*i)
        {
            return false;
        }
    }
    return true;
}

int main(){
    int *a;
    const int N=10000;
    size_t size=N*sizeof(int);

    cudaMallocManaged(&a,size);
    cpu(a,N);
    size_t threads=256;
    size_t blocks=1;
    gpu<<<blocks,threads>>>(a,N);
    cudaDeviceSynchronize();

    checkout(a,N)?printf("ok\n"):printf("error\n");

    cudaFree(a);

}
```

## 异常处理

```c++
cudaGetLastError() 处理kenerl错误
#include <stdio.h>
#include <assert.h>

inline cudaError_t checkCuda(cudaError_t result){
    if (result!=cudaSuccess)
    {
        fprintf(stderr,"cuda runtime error: %s \n",cudaGetErrorString(result));
        assert(result==cudaSuccess);
    }
    return result;
}


void cpu(int *a,int N){
    for (int i = 0; i < N; ++i)
    {
        a[i]=i;
    }
}

__global__ void gpu(int *a,int N){
    int threadi=blockIdx.x*blockDim.x+threadIdx.x;
    int sumthread=gridDim.x*blockDim.x;
    for (int i = threadi; i < N; i+=sumthread)
    {
        a[i]*=2;
    }
    
}

bool checkout(int *a,int N){
    for (int i = 0; i < N; ++i)
    {
        if (a[i]!=2*i)
        {
            return false;
        }
    }
    return true;
}

int main(){
    int *a;
    const int N=10000;
    size_t size=N*sizeof(int);

    cudaMallocManaged(&a,size);
    cpu(a,N);
    size_t threads=256;
    size_t blocks=1;
    gpu<<<blocks,-1>>>(a,N);
    checkCuda(cudaGetLastError());
    cudaDeviceSynchronize();

    checkout(a,N)?printf("ok\n"):printf("error\n");

    cudaFree(a);

}
cuda runtime error: invalid configuration argument 
exceptionHandle: exceptionHandle.cu:8: cudaError_t checkCuda(cudaError_t): Assertion `result==cudaSuccess' failed.
Aborted (core dumped)
```

## 矩阵加法

```c++
#include<stdio.h>

#define N 64

void cpu(int *a,int *b,int *c_cpu){
    for (int r = 0; r < N; ++r)
    {
        for (int c = 0; c < N; ++c)
        {
            c_cpu[r*N+c]=a[r*N+c]+b[r*N+c];
        }
    }
}
__global__ void gpu(int *a,int *b,int *c_gpu){
    int idx_x=blockDim.x*blockIdx.x+threadIdx.x;
    int idx_y=blockDim.y*blockIdx.y+threadIdx.y;
    if (idx_x<N&&idx_y<N)
    {
        c_gpu[idx_x*N+idx_y]=a[idx_x*N+idx_y]+b[idx_x*N+idx_y];
    }
    
}
bool check(int *c_cpu,int *c_gpu){
    for (int r = 0; r < N; ++r)
    {
        for (int c = 0; c < N; ++c)
        {
            if (c_cpu[r*N+c]!=c_gpu[r*N+c])
            {
                return false;
            }
            
        }
    }
    return true;
}

int main(){
    int *a,*b,*c_cpu,*c_gpu;
    size_t size =N*N*sizeof(int);
    cudaMallocManaged(&a,size);
    cudaMallocManaged(&b,size);
    cudaMallocManaged(&c_cpu,size);
    cudaMallocManaged(&c_gpu,size);
    // init
    for (int r = 0; r < N; ++r)
    {
        for (int c = 0; c < N; ++c)
        {
            a[r*N+c]=r;
            b[r*N+c]=c;
            c_cpu[r*N+c]=0;
            c_gpu[r*N+c]=0;
        }
    }

    dim3 threads(16,16,1);
    dim3 blocks((N+threads.x-1)/threads.x,(N+threads.y-1)/threads.y,1);
    cpu(a,b,c_cpu);
    gpu<<<blocks,threads>>>(a,b,c_gpu);
    cudaDeviceSynchronize();
    check(c_cpu,c_gpu)?printf("ok\n"):printf("error\n");
    cudaFree(a);
    cudaFree(b);
    cudaFree(c_cpu);
    cudaFree(c_gpu);

}
```

## 性能分析

```
nsys profile --stats=true ./matrix
```

## GPU属性信息

```c++
#include <stdio.h>

int main()
{

    int id;
    cudaGetDevice(&id);
    cudaDeviceProp props;
    cudaGetDeviceProperties(&props, id);

    printf(" device id is : %d\n sms is : %d\n capatity major is : %d\n capatity minor is : %d \n warp size is : %d\n",
     id, props.multiProcessorCount,props.major,props.minor,props.warpSize);
}
```

统一内存分配(UM)

```
 cudaMallocManaged()
 分配UM时，内存尚未驻留在主机或设备上，主机或设备尝试访问内存时会发生页错误，此时主机或设备会批量迁移所需的数据，同理当CPU或加速系统中任何GPU尝试访问尚未驻留在其上的内存时，会发生页错误并触发迁移。
 预取cpu内存分配给显存
 cudaMemPrefetchAsync()
```

手动分配内存

```c++
int *host_a,*device_a;
cudaMalloc(&device_a,size); 分配显存
initHost(host_a,N); 初始化内存
cudaMemcpy(device_a,host_a,size,cudaMemcpyHostToDevice); 复制内存到显存
kernel<<<blocks,threads,0,someStream>>>(device_a,N); 启动核函数
cudaMemcpy(host_a,device_a,size,cudaMemcpyDeviceToHost); 复制显存到内存
verifyOnHost(host_a,N); 验证数据
cudaFree(device_a); 释放显存
cudaFreeHost(host_a); 释放内存
```

## 流

## <img src="images\cuda_stream.png" alt="image-20220211162609408" style="zoom:80%;" />

## 共享内存

![image-20220211164413603](images\share_memory.png)

## 前缀

![image-20220214145026592](images\func_prefix.png)

## 线程层次

<img src="images\thread.png" alt="image-20220214160414462" style="zoom:80%;" />

<img src="images\thread_exe.png" alt="image-20220214161158610" style="zoom:80%;" />



## memory

<img src="images\memory.png" alt="image-20220217105115805" style="zoom:80%;" />

<img src="images\memeroy_detail.png" alt="image-20220217191655478" style="zoom:80%;" />

## 内存模型

![image-20220304093943266](images\内存模型.png)

### 寄存器register

```
nvcc -Xptxas -v hello.cu
```

可以查询当前cu文件，使用了多少寄存器、全局内存、常量内存、以及共享内存

### local memory

```c++
#include <stdio.h>
#include <cuda_runtime.h>

using namespace std;

static __global__ void test_kernel()
{
    int array[3];
    float value = 5;
    __shared__ int share_value;
    printf("array is local = %s\n", __isLocal(array) ? "true" : "false");
    printf("value is local = %s\n", __isLocal(&value) ? "true" : "false");
    printf("share_value is local = %s\n", __isLocal(&share_value) ? "true" : "false");

}
void local_memory(){
    test_kernel<<<1,1>>>();
    cudaDeviceSynchronize();
}
int main(){
    local_memory();
    return 0;
}
array is local = true
value is local = true
share_value is local = false
```

### shared memory

**静态共享内存**

```c++
#include<iostream>


using namespace std;


// 方式2：声明共享变量，不能给初始值，需要由线程来初始化。
__shared__ int shared_value2;

__global__ void test_kernrl(){
    // 方式1：声明静态大小的共享内存，所在block线程公用
    __shared__ int shared_array[8];

    // 方式2 声明共享变量，不能给初始值，需要线程来初始化(核函数内外都可以声明)
    __shared__ int shared_value1;

    if (threadIdx.x==0)
    {
        shared_value1=5;
        shared_value2=8;
        shared_array[0]=33;
    }
    // block内部用于线程同步
   // 就是同一block内所有线程执行至__syncthreads()处等待全部线程执行完毕后再继续
    __syncthreads();
    // __syncwarp()同步warp内线程

    printf("%d. shared_value1=%d,shared_value2=%d\n",threadIdx.x,shared_value1,shared_value2);
    printf("%d. shared_array[0]=%d\n",threadIdx.x,shared_array[0]);
    
}
int main(){
    test_kernrl<<<2,2>>>();
    cudaDeviceSynchronize();
    return 0;
}
0. shared_value1=5,shared_value2=8
1. shared_value1=5,shared_value2=8
0. shared_value1=5,shared_value2=8
1. shared_value1=5,shared_value2=8
0. shared_array[0]=33
1. shared_array[0]=33
0. shared_array[0]=33
1. shared_array[0]=33
    
定义静态大小共享内存，共享内存在block内，所有线程可见
```

**动态共享内存**

```c++
#include<iostream>
using namespace std;

__global__ void test_kernel(){

    // 方式3：使用extern声明外部的动态大小共享内存，由启动核函数的第三个参数指定
    extern __shared__ int shared_array[];
    if (threadIdx.x==0)
    {
        shared_array[0]=blockIdx.x;
    }
    __syncthreads();

    printf("blockidx=%d, threadidx=%d, shared_array[0]=%d\n",blockIdx.x,threadIdx.x,shared_array[0]);
    

}

int main(){
    test_kernel<<<2,2,sizeof(int)*5>>>();
    cudaDeviceSynchronize();
    return 0;
}
blockidx=1, threadidx=0, shared_array[0]=1
blockidx=1, threadidx=1, shared_array[0]=1
blockidx=0, threadidx=0, shared_array[0]=0
blockidx=0, threadidx=1, shared_array[0]=0
```

### global memory

```c++
#include<iostream>

using namespace std;


// 方式2：__device__定义
__device__ float device_global_array[100];

__global__ void test_kernel(float* host_global){
    printf("device_global_array = %s\n",__isGlobal(device_global_array)?"true":"false");
    printf("host_global is global =%s\n",__isGlobal(host_global)?"true":"false");
}

int main(){

    // 方式1：主机分配
    float* host_global = nullptr;
    cudaMalloc(&host_global,sizeof(float)*100);

    test_kernel<<<1,1>>>(host_global);
    cudaDeviceSynchronize();
    return 0;
}
device_global_array = true
host_global is global =true
```

![image-20220304140626598](images\cpu_gpu.png)

页锁定内存

![image-20220304141632625](images\pin_memory.png)

pinned memory 依然是host memory 它的使用场景是代替malloc、new然后进行cudaMemcpy

虽然pinned memory可以允许GPU直接访问，但是这种操作效率是低效的，正确的使用场景是适合大数据传输的中间存储，不适合频繁的读写操作

```c++
#include<iostream>
using namespace std;

__global__ void test_kernel(float* array){
    array[threadIdx.x]=threadIdx.x;
}

int main(){

    int num=5;
    float* array=nullptr;
    cudaMallocHost(&array,sizeof(float)*num);
    test_kernel<<<1,num>>>(array);
    cudaDeviceSynchronize();
    for (int i = 0; i < num; ++i)
    {
        printf("array[%d]=%f\n",i,array[i]);
    }
    cudaFreeHost(array);
    return 0;
}
array[0]=0.000000
array[1]=1.000000
array[2]=2.000000
array[3]=3.000000
array[4]=4.000000
```

![image-20220304143333142](images\alloc_memory.png)

![image-20220304143643259](images\zeo_copy.png)

**统一内存管理**

![image-20220304152505684](images\unified_memory.png)

<img src="images\unified_code.png" alt="image-20220304152701314" style="zoom:80%;" />

### constant memory

```c++
#include<iostream>
using namespace std;

// 直接定义和初始化(可选)

__constant__ float warp_matrix[6]={1,2,3,4,5,6};

__global__ void test_kernel(){
    // 在核函数内，constant内存不能修改，否则报错
    printf("warp_matrix[%d] = %f\n",threadIdx.x,warp_matrix[threadIdx.x]);
}

int main(){
    // 覆盖warp_matrix的初始化值定义
    float host_warp_matrix[6]={6,5,3,1,6,2};
    cudaMemcpyToSymbol(warp_matrix,host_warp_matrix,sizeof(host_warp_matrix));

    test_kernel<<<1,6>>>();
    cudaDeviceSynchronize();
}
warp_matrix[0] = 6.000000
warp_matrix[1] = 5.000000
warp_matrix[2] = 3.000000
warp_matrix[3] = 1.000000
warp_matrix[4] = 6.000000
warp_matrix[5] = 2.000000
nvcc -Xptxas -v constant_memory.cu
ptxas info    : 22 bytes gmem, 24 bytes cmem[3]
ptxas info    : Compiling entry function '_Z11test_kernelv' for 'sm_30'
ptxas info    : Function properties for _Z11test_kernelv
    16 bytes stack frame, 0 bytes spill stores, 0 bytes spill loads
ptxas info    : Used 8 registers, 320 bytes cmem[0]
```

## 线程束(warp)

SIMT(single instruction multiple thread) 单指令多线程，cuda执行模型

SIMD(single instruction multiple data) 单指令多数据，多在cpu流指令集使用

<img src="images\simd1.png" alt="image-20220304162556213" style="zoom:80%;" />

<img src="images\simd2.png" alt="image-20220304162709696" style="zoom:80%;" />

**warp 和 thread blocks**

<img src="images\warp1.png" alt="image-20220304163516050" style="zoom:80%;" />

<img src="images\warp2.png" alt="image-20220304163831251" style="zoom:80%;" />

```c++
#include<iostream>


__global__ void test_kernel(int* array){

    array[threadIdx.x]=threadIdx.x;
    printf("step1 %d\n",threadIdx.x);
    printf("step2 %d\n",threadIdx.x);
    printf("step3 %d\n",threadIdx.x);
}

int main(){
    const int num=5;
    int* array=nullptr;
    cudaMallocManaged(&array,sizeof(float)*num);

    test_kernel<<<1,num>>>(array);
    cudaDeviceSynchronize();
    for (int i = 0; i < num; ++i)
    {
        printf("array[%d]=%d\n",i,array[i]);
    }
    return 0;
}
step1 0
step1 1
step1 2
step1 3
step1 4
step2 0
step2 1
step2 2
step2 3
step2 4
step3 0
step3 1
step3 2
step3 3
step3 4
array[0]=0
array[1]=1
array[2]=2
array[3]=3
array[4]=4
```

reason 时钟周期

<img src="images\warp_time.png" alt="image-20220304165217327" style="zoom:80%;" />

<img src="images\warp_exec.png" alt="image-20220304165550675" style="zoom:80%;" />

<img src="images\warp_if.png" alt="image-20220304170332181" style="zoom:80%;" />

**数组求和**

问题，给定具有n个元素的数组array，对所有元素求和并打印

思路：

![image-20220307135255320](images\sum.png)

<img src="images\sum_structure.png" alt="image-20220307135346495" style="zoom:80%;" />

代码实现：

```

```

**指令延迟**

<img src="images\delay.png" alt="image-20220304172206528" style="zoom:80%;" />

<img src="images\delay2.png" alt="image-20220304172349698" style="zoom:80%;" />

性能总结

<img src="images\summary.png" alt="image-20220304172652648" style="zoom:80%;" />

