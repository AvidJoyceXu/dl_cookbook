# Learning MPI 【20240507】
- reference: https://zhuanlan.zhihu.com/p/355652501
## Basic Information
- MPI - 接口
- implementation: OpenMPI / MPICH
## Installing MPI
- 比如你安装了两个版本的gcc编译器，你就需要去设置环境变量，告诉系统当你调用gcc的时候你到底想调用哪个版本的gcc，把你想调用的那个gcc的路径和gcc绑定起来。
（没亲手装过，直接用的超算队的服务器，load module就可以直接用）

## MPI's six basic functions
https://zhuanlan.zhihu.com/p/357551507
```Fortran
call MPI_INIT() # Fortran 
```
```C
int main(int *argc,char* argv[]) 
{ 
    MPI_Init(&argc,&argv); 
}
```

```cpp
MPI_Finalize() //C++ 
```

```cpp
int MPI_Comm_Rank(MPI_Comm comm, int *rank) 
```
- store the pid in variable **rank**
- MPI_Comm: predefined variable such as `MPI_COMM_WORLD`
```cpp
int MPI_Comm_Size(MPI_Comm, int *size) 
```
- get how many processes are there in the MPI_Comm
```cpp
int MPI_Send(type* buf, int count, MPI_Datatype, int dest, int tag, MPI_Comm comm)
```
- buf: the address of the array(通常为数组)
- count: count个变量
- MPI_Datatype: `MPI_INT` for example
- dest: the **pid** for the receiving process
- tag: message tag to distinguish different messages sending between the same source and dest
```cpp
int MPI_Recv(type* buf, int count, MPI_Datatype, int source, int tag, MPI_Comm comm, MPI_Status *status) 
```
- source: the pid signifying which process to **hear from**
- status: 通过`status.MPI_SOURCE`，`status.MPI_TAG`和`status.MPI_ERROR`的方式调用这三个信息
```c
    MPI_Reduce(&mypi, &pi, 1, MPI_DOUBLE, MPI_SUM, 0, MPI_COMM_WORLD);
```
- 规约计算，用于对多进程的计算结果进行规约。规约定义为MPI_SUM等等。
```fortran
call MPI_BCAST(b,cols,MPI_DOUBLE_PRECISION,master,MPI_COMM_WORLD,ierr) 
```
## Others
### 虚拟进程
- sentinel: clarify code for boundary checking
`MPI_PROC_NULL`是一个假想的进程，其存在有助于编写时的方便。当一个真实进程向虚拟进程发送和接收数据时，会立刻执行一个空操作。
## MPI commands
### compiling MPI
> The error `undefined reference to 'MPI_Init'` typically occurs when the MPI library is not properly linked during the compilation of an MPI program. To resolve this issue, you need to ensure that the MPI compiler wrappers are used for compiling and linking your program.

use `mpicc` for C programs or `mpic++` for C++ programs

## allocate multiple processes
- `mpriun -np <number> <object file>`

## Parallel Computing:  Typical Templates
### SPMD: Jacobi iteration
通俗的讲，Jacobi迭代就是用上下左右周围的四个点取平均值来得到新的点，即
![[Pasted image 20240507135148.png]]
每一轮迭代计算所用的数值都是上一轮的结果，即还需要额外声明一个变量来储存当前这一轮迭代的结果，然后在迭代完成后刷新矩阵A的值。
#### Inter-process Communication
- 在**阻塞通信**前提下，需要匹配send和receive，避免缓冲区泄漏（同时send）或死锁（同时receive）
- MPI_SENDRECV(未确定它的Cpp接口)
### Matrix Multiplication
在逻辑上规定一个主进程，用于将数据发送给各个进程，再收集各个进程所计算的结果。