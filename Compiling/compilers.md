# Play with compilers

# Flags: `-I, -L, -l`
- **`-I/path/to/netcdf-fortran/include`**:
    
    - The `-I` flag is used to specify an **include directory**. This tells the compiler where to look for header files during the compilation process. In your case, `-I/path/to/netcdf-fortran/include` tells the compiler to **search for header files** in the directory `/path/to/netcdf-fortran/include`.
- **`-L/path/to/netcdf-fortran/lib`**:
    
    - The `-L` flag is used to specify a **library directory**. This informs the linker where to look for **libraries** during the **linking stage** of the compilation. The flag `-L/path/to/netcdf-fortran/lib` tells the linker to search for library files in the directory `/path/to/netcdf-fortran/lib`.
- **`-lnetcdff`**:
    
    - The `-l` flag is used to specify a **library to link against**. When you use `-lnetcdff`, the linker searches for a library named `libnetcdff.a` or `libnetcdff.so` in the directories specified by `-L` flags (and the default library paths). The `-l` flag helps the linker understand which specific libraries your program depends on.

# GCC: GNU Compiler Collection
## `-g 03`
The `-g` and `-O3` flags are compiler options used primarily with GCC (GNU Compiler Collection) to control different aspects of the compilation process:

1. **`-g`**:
    
    - The `-g` flag tells the compiler to include debugging information in the compiled executable. This information is used by debuggers (like `gdb`) to allow you to step through your code, inspect variables, and see where errors occur in your source code. The debugging information includes things like line numbers and variable names, which help in understanding the execution flow during debugging sessions. The `-g` flag does not affect the optimization level of the compilation.
2. **`-O3`**:
    
    - The `-O3` flag is an optimization flag that tells the compiler to apply a high level of optimization to the code. `-O3` enables all the optimizations provided by `-O2` (a lower level of optimization) and adds more aggressive optimizations, such as function inlining, loop unrolling, and vectorization. These optimizations aim to improve the performance of the compiled code, potentially at the cost of longer compilation times and larger executable size. However, it should be noted that highly aggressive optimizations can sometimes introduce subtle bugs or make debugging more difficult.

In summary, using `-g` allows you to debug your program, while `-O3` optimizes the program for maximum performance. These two flags can be used together if you want both debugging information and optimized code, though it can make debugging more challenging since optimizations may alter the structure of the generated code.
# Make
`make -j192 2>&1 | tee make.log
- `-j`: parallel job
- `2>&1`: This part of the command redirects standard error (file descriptor `2`) to standard output (file descriptor `1`).
- **`2>&1`**: Redirects standard error to the same place as standard output.
- **`2>1`**: Redirects standard error to a file named `1`
- `tee`: **redirect the output to a file** as well as **print it on standard output**


