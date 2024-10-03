# Linux and compiling

# Make
![[Pasted image 20240517193120.png]]
- gcc: compiler driver
`cmake, xmake`: generate makefiles, and generate a VSCode profiles
![[Pasted image 20240517193107.png]]
- make: 
命令执行器。
分析依赖状态，同时决定命令是否要重新执行一遍（可能不需要）
![[Pasted image 20240517193822.png]]
![[Pasted image 20240517194052.png]]
- 语法糖： 定义编译选项，复用。
![[Pasted image 20240517194035.png]]
- 语法糖：Pattern Rules
所有`.o`文件，都依赖于同名`.c`
![[Pasted image 20240517194258.png]]
- 语法糖：Phony Targets (do not be compile/generate, just to run some commands)
![[Pasted image 20240517194425.png]]
- autoconf -> cmake -> xmake

## Configure: environment configuration
![[Pasted image 20240517194636.png]]
### CMake: 有自己的编程语言
![[Pasted image 20240517194926.png]]
- CMake语法
![[Pasted image 20240517194912.png]]
![[Pasted image 20240517195107.png]]

![[Pasted image 20240517195421.png]]
- cmake的嵌套调用 
![[Pasted image 20240517195558.png]]