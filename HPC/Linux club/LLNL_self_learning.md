# LLNL self learning

## Reference
- lawrence livermore national laboratory
https://hpc.llnl.gov/documentation/tutorials

## Introduction to parallel computing
- "Grand Challenge Problems" ([en.wikipedia.org/wiki/Grand_Challenge](https://en.wikipedia.org/wiki/Grand_Challenges)) requiring petaflops and petabytes of computing resources.
### Taxonomy
- Instruction stream & data stream
#### SIMD: single instruction multiple data
 - example: GPU processing unit for graphics
![[Pasted image 20240420125039.png]]
#### MIMD
- most common type of parallel computer
![[Pasted image 20240420125237.png]]
### Terminology
- CPU: might be mutlicore, might be organized into muliple sockets, 
	- hardware infrastructure support memory sharing across sockets
- node: **a standalone 'computer in a box'**
	- nodes are networked together to comprise a supercomputer
- shared memory: all processors have direct access to common physical memory
- distributed memory: tasks can only logically "see" local machine memory and must use communications to access memory on other machines where other tasks are executing.
- computational **granularity**: measuring the ratio of computation to communication
	- _**Coarse:**_ relatively large amounts of computational work are done between communication events
	- **_Fine:_** relatively small amounts of computational work are done between communication events
- parallel overhead
	- Task start-up time
	- Synchronizations
	- Data communications
	- Software overhead imposed by parallel languages, libraries, operating system, etc.
	- Task termination time
- scalability: ability to demonstrate a proportionate increase in parallel speedup with the addition of more resources
### Portability
- OS - related
- Hardware architectures
### Resource Requirements
- wall clock time VS CPU time: the former is measuring **actual time**, just as its name implies
- memory cost
- short run: overheads including environment setting up, task creation, communication and task termination
## Memory Architectures
### Shared Memory
#### Uniform Memory Access
- represented mostly by **Symmetric Multiprocessor** machines
- identical processors
- equal access and access times to memory
#### Non-Uniform Memory Access
### Distributed Memory
- synchronization: dependent on programmer
	- tailor to the details regarding data communication
	- network communications are required to **move data from one machine to another**.
- non-uniform memory access times
	- data residing on a remote node takes longer to access than node local data.
## Parallel Programming Models
- abstraction betwwen user-interface and hardware architecture
### Shared Memory Model without threads
- a shared address space
- locks/semaphores: control access to the shared memory
- difficult to manage **data locality**
	- conserve memory access and cache refreshes
### Thread Model
- Threads communicate with each other through global memory (updating address locations). This requires synchronization constructs to ensure that **more than one thread is not updating the same global address** at any time.
![[Pasted image 20240420164055.png]]
- POSIX Threads tutorial: [hpc.llnl.gov/sites/default/files/2019.08.21.TAU_.pdf](https://hpc.llnl.gov/sites/default/files/2019.08.21.TAU_.pdf
- OpenMP tutorial: [hpc-tutorials.llnl.gov/openmp/](https://hpc-tutorials.llnl.gov/openmp/)
### Distributed Memory / Message Passing Model
- Tasks exchange data through communications by sending and receiving messages.
- Data transfer usually requires cooperative operations to be performed by each process. For example, a **send** operation must have a matching **receive** operation
- All MPI specifications are available on the web at [http://www.mpi-forum.org/docs/](http://www.mpi-forum.org/docs/).
- MPI tutorial: [hpc-tutorials.llnl.gov/mpi/](https://hpc-tutorials.llnl.gov/mpi/)
### Hybrid Model
- eg: the combination of the message passing model (MPI) with the threads model (OpenMP).
	- Threads perform computationally intensive kernels using **local, on-node data**
	- Communications between processes on **different nodes** occurs over the network using MPI
## Designing Parallel Programs
### Programmer Directed
- using "compiler directives" or possibly compiler flags: explicitly tell the compiler how to parallelize the code
### Understand the Problem
- hotspot: performance analysis
- bottleneck: restrict parallelism
- optimized third party parallel software
### Partitioning
- break the problem into discrete chunks of work
#### Functional Decomposition
- focus on the computation that is to be performed
![[Pasted image 20240420173337.png]]
- examples: signal processing
![[Pasted image 20240420173437.png]]

### Synchronous vs. asynchronous communications
- synchronous: blocking communications
- asynchronous communication: other work can be done when communications are taking place
	- interleave computation with communication
![[Pasted image 20240420174653.png]]
### Load Balance
### Dynamic Work Assignment
- Certain classes of problems result in load imbalances even if data is evenly distributed among tasks
![[Pasted image 20240420181954.png]]
- **scheduler-task pool**
![[Pasted image 20240420182035.png]]
## I/O
![[Pasted image 20240420182146.png]]
### Parallel file systems are available
## Debugging
- The good news is that there are some excellent debuggers available to assist:
    - Threaded - pthreads and OpenMP
    - MPI
    - GPU / accelerator
    - Hybrid
- Livermore Computing users have access to several parallel debugging tools installed on LC's clusters:
    - TotalView from RogueWave Software
    - DDT from Allinea
    - Inspector from Intel
    - Stack Trace Analysis Tool (STAT) - locally developed at LLNL
- All of these tools have a learning curve associated with them.
- For details and getting started information, see:
    - LC's web pages at [hpc.llnl.gov/software/development-environment-software](https://hpc.llnl.gov/software/development-environment-software)
    - TotalView tutorial: [hpc.llnl.gov/documentation/tutorials/totalview-tutorial](https://hpc.llnl.gov/documentation/tutorials/totalview-tutorial)
## Performance Analysis and Tuning
