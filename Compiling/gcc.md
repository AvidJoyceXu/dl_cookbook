# gcc

GCC (GNU Compiler Collection) provides a rich set of options to customize the compilation process for C, C++, and other supported languages. Here's an overview of commonly used GCC compilation options, categorized by purpose:

## Basic Compilation Options
- -c: Compile source files into object files without linking.
- -o `<output_file>`: Specify the name of the output file.
- -S: Generate assembly code instead of an object file.
- -E: Preprocess the source code only and output the result.

## Optimization Options

- -O0: No optimization (default).
- -O1: Optimize for code size and execution time moderately.
- -O2: Further optimization for performance without increasing compilation time significantly.
- -O3: Maximum optimization, including aggressive inlining and loop unrolling.
- -Ofast: Similar to -O3 but disregards strict standards compliance.
- -Os: Optimize for size.
- -march=`<arch>`: Generate code optimized for a specific architecture (e.g., -march=native).
- -mtune=`<arch>`: Optimize performance for a specific CPU without altering compatibility.

## Debugging and Profiling
- -g: Include debugging information in the compiled output.
- -pg: Enable profiling with gprof.
- -fsanitize=address: Enable AddressSanitizer for detecting memory errors.
- -fsanitize=undefined: Enable UndefinedBehaviorSanitizer for finding undefined behavior.

## Warnings and Errors
- -Wall: Enable most common warnings.
- -Wextra: Enable additional warnings beyond -Wall.
- -Werror: Treat warnings as errors.
- -pedantic: Enforce strict ISO C/C++ compliance.
- -fsyntax-only: Check for syntax errors without generating code.

## Linking and Libraries
- -L`<path>`: Add a directory to the library search path.
- -l`<library>`: Link against a specific library (e.g., -lm for math library).
- -shared: Create a shared library.
- -static: Force static linking.
- -rdynamic: Include all symbols in the dynamic symbol table (useful for debugging).

## Preprocessor Options
- -I`<dir>`: Add a directory to the include file search path.
- -D`<macro>`: Define a macro for the preprocessor.
- -U`<macro>`: Undefine a macro.

## Code Generation
- -fPIC: Generate position-independent code (useful for shared libraries).
- -fstack-protector: Enable stack smashing protection.
- -fopenmp: Enable OpenMP for parallel programming.
- -pthread: Enable multithreading support with pthreads.
  
## Language Standards
- -std=`<standard>`: Specify the language standard (e.g., -std=c11, -std=c++17).
- -ansi: Enforce strict ANSI C compliance (equivalent to -std=c89 and -pedantic).

## Advanced Options
- -ffast-math: Enable unsafe floating-point optimizations.
- -fno-exceptions: Disable exceptions in C++.
- -fno-rtti: Disable runtime type information (RTTI) in C++.
- -flto: Enable link-time optimization for better performance.
