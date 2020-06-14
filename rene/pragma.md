# Pragmas
#### Written by Rene on 12 June, 2020

## A little bit about this blog and why I made it (since it's my first post)
Welcome to my first post. My goal with this blog is to do a weekly post about something new I learned. Since I have no formal education in computer science and have only been programming for a bit over a year, I decided it was a good idea to start documenting the things I learn in an effort to retain information better. Writing about something is a good way to solidify information and doing it transparently is great because I can be accountable to my goal to write weekly.

Since this "blog" is open to the public, if I happen to inaccurately represent some information, let me know either by opening an issue or a PR to fix it.

So, let's begin.

## This week, I learned about **pragmas**. 
I came across the concept of a pragma when looking over a [PR to add BLS precompiles](https://github.com/ethereum/go-ethereum/pull/21018) that was recently merged into [geth](https://github.com/ethereum/go-ethereum). Specifically, this part of the code caught my eye: 

```go
//go:noescape
func add(c, a, b *fe)
```

I didn't understand the purpose of the comment `//go:noescape`. Even though I've encountered compiler directives before (but just didn't know the term for it) in the form of go [build tags](https://dave.cheney.net/2013/10/12/how-to-use-conditional-compilation-with-the-go-build-tool), I didn't put two and two together that it's possible to tell the compiler how to interpret source code. In retrospect, it kind of seems like a no-brainer, but I'll summarize the concept of a pragma (a.k.a. compiler directive).

## What's a pragma?
A compiler directive, or "pragma", is just a way to specify within the source code how you'd like your code to be interpreted by the compiler. Without compiler directives, the compiler will just interpret your source code as usual.

Pragmas are useful for a plethora of reasons, but a good example of where a compiler directive could be useful is if you really want to ensure that memory is released after the completion of a function / that a memory leak does not occur.This is what the `//go:noescape` directive does — it directs the compiler to ensure that memory used by the following function does not "escape to the heap". "Escaping to the heap" is another way of saying that the memory allocated for the function (stack or memory that is local to the function and is managed quickly and efficiently by the CPU) does not get released immediately after the function is completed, but is rather pushed to the "heap", which is the memory that the CPU manages a bit more slowly and less efficiently than that of the stack since it is "global" to the program.

Another example where using a compiler directive is useful is where inlining is possible. Inlining can optimize the efficiency of a program by copying the code of the inlined function into wherever it is called, reducing the function call overhead and also the amount that has to be pushed off or popped on the stack. Note that inlining is usually typically only useful for smaller functions where the overhead of the function call is greater than executing the function itself.

## TL;DR
Sometimes telling the compiler how to interpret your code results in optimizations.

##### Cites:
* https://dave.cheney.net/2018/01/08/gos-hidden-pragmas

