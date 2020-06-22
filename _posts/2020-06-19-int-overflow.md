---
layout:        post
title:         Integer Overflows in Golang
date:          2020-06-22 16:51:00
summary:       TL;DR on Integer Overflows in Go
categories:    blog
tags:          golang compiler integers
---

## This week, I learned about **integer overflows** in Go.
Integer overflows are another concept I've encountered briefly when trying to [implement](https://github.com/renaynay/go-canonical-serialization) canonical serialization in Go, but never fully understood how such instances are handled and how they're handled differently between languages. For this post specifically, I'll focus on how they're handled in Go.

## What is an integer overflow? 
An integer overflow occurs when a value does not fit within its allocated memory space. This can occur for a variety of reasons, but an integer overflow should almost always be handled properly since they can cause unexpected or incorrect behavior and can pose serious security risks for a program. Integer overflows are especially risky when dealing with financial technologies like blockchain where the financial consequence is huge if integer overflow occurs and is not properly handled.

## How is integer overflow handled (in Go)?
There are a few options when it comes to dealing with integer overflow in Go. One option is to ignore it since the Go compiler doesn't check for overflow, similarly to C, C++ and Java. The following program will still work despite a clear integer overflow: 

```go
func main() {
	var foo uint64 = 18446744073709551615 // Maximum possible value of a 64-bit unsigned integer
	foo = foo + uint64(4)
	// outputs 3
	
	var bar uint64 = 18446744073709551615
	bar = bar*2
	// outputs 18446744073709551614
}
```

Neither of the output values is "correct" in the traditional sense of mathematics, but the Go compiler handles overflow in this manner. The option to ignore an integer overflow is extremely unsafe, but it could be the best option for some programs in which integer overflow is highly unlikely. I'm not really sure where that could be the case, but nonetheless, it could exist.

Another option is to use an [overflow library](https://github.com/JohnCGriffin/overflow) or to implement individual overflow checks, like [this](https://stackoverflow.com/questions/33641717/detect-signed-int-overflow-in-go): 

```go
package main

import (
    "errors"
    "fmt"
    "math"
)

var ErrOverflow = errors.New("integer overflow")

func Add32(left, right int32) (int32, error) {
    if right > 0 {
        if left > math.MaxInt32-right {
            return 0, ErrOverflow
        }
    } else {
        if left < math.MinInt32-right {
            return 0, ErrOverflow
        }
    }
    return left + right, nil
}
func main() {
    var a, b int32 = 2147483327, 2147483327
    c, err := Add32(a, b)
    if err != nil {
        // handle overflow
        fmt.Println(err, a, b, c)
    }
}
```

In either case, if an integer overflow is found during this check, it is possible to either panic or handle the error differently. Either way, it's good practice to ensure that integer overflows aren't possible in the program.

## What if you need an integer larger than 64 bits? 
If a program needs an integer larger than 64 bits, then there is a native Go library, [math/big](https://golang.org/pkg/math/big/), that allows for mathematical operations on integers of an arbitrary size. The [go-ethereum client](https://github.com/ethereum/go-ethereum/search?q=uint256&unscoped_q=uint256) is a good example of a program that frequently uses unsigned integers larger than 64 bits, with the largest uint as big as 256 bits. Why? I do not particularly understand, but whatever. Here is a possible [explanation](https://ethereum.stackexchange.com/questions/363/why-is-ether-divisible-to-18-decimal-places/499#499). ;)

For the Geth client in particular, the math/big library was not efficient enough with 256-bit uints, so members from the team developed a [uint256 library](https://github.com/holiman/uint256) meant to increase the efficiency for operations on 256-bit uints in the [Ethereum Virtual Machine](https://medium.com/mycrypto/the-ethereum-virtual-machine-how-does-it-work-9abac2b7c9e). 

## TL;DR
Sometimes programs either use or can encounter integers larger than 64 bits and such instances need to be dealt with carefully.

#### Cites
* https://medium.com/@griffinish/integer-overflow-in-golang-9e13e274c8a5
