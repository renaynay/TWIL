# Pragmas, Macros, etc.
#### Author: Rene
#### Date: 12 June 2020

Links: 
* https://dave.cheney.net/2018/01/08/gos-hidden-pragmas
* https://www.computerhope.com/jargon/m/macro.htm
* https://ahmadawais.com/pragma-mean-programming/

Notes: 
* stack vs. heap: stack is local to function (memory is easily released after completion of function) whereas heap means variable escapes the function to the heap, in which case memory is not managed automatically or as efficiently by the CPU). This means you could get a memory leak where memory is set aside and does NOT become availble to other processes. Heap is slower to read from (must use pointers to access that memory). Kind of like the concept of global vs. local but has to do with the way memory is managed by the computer.
* source code can be interpreted in different ways by the compiler. If you include something called a "compiler directive" (aka "pragma"), then you can direct the compiler to interpret some code in a certain way.
* "inlining" is when the compiler replaces a function call site with the body of the called function. Macro expansion and inline expansion both occur during compilation, but inlining is different in that it doesn't change the source code while macro expansion occurs BEFORE compilation, which results in different text that is then processed by the compiler... What are the usecases for both? Macro vs. Inline?
* malloc, calloc, free, realloc:
	* malloc (memory allocation) = dynamically allocate a single large block of memory with specified size, returns pointer with default garbage value (nil for go?)
	* calloc (contiguous allocation) = dynamically allocate specified number of blocks of memory, init each block w/ default value 0
	* free = free up memory
	* realloc (re-allocation) = change memory allocation of previously allocated memory
