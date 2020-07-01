---
layout:        post
title:         Atomic Counters
date:          2020-07-01 17:00:00
summary:       What are Atomic Counters?
categories:    blog
tags:          golang goroutines threads
---
# What are atomic counters?
Before talking about atomic counters, I want to define atomic operations and atomicity. An operation is atomic if at no point can the operation be divided -- it happens instantaneously and no other process can access the object that's being operated on in the midst of the operation. In other words, atomic operations are individisble. There is only a before and after, and no in-between where the in-flux state can be accessed.

## Why is atomicity important?
Atomicity is important when dealing with concurrent operations that access and modify shared objects. To guarantee that the program works as intended, it's important to ensure that the shared objects can only be operated on atomically or that that object can be locked while a process operates on it, and subsequently unlocked once that process is finished.

## How can you guarantee atomicity?
One way to guarantee atomicity is to use a [`mutex`](https://tour.golang.org/concurrency/9) that can `lock` and `unlock` access to some place in memory. Here's an example of how a mutex can be used in Go using the [sync package](https://godoc.org/sync):

```go
// SafeCounter is safe to use concurrently.
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mux.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}
```

You can run this program [here](https://tour.golang.org/concurrency/9).

Another way to ensure atomicity is to use an [atomic counter](https://gobyexample.com/atomic-counters) provided by the [sync/atomic](https://golang.org/pkg/sync/atomic/) package.Note that this is only relevant when dealing with a single value, like an integer. Atomic operations like those provided by the sync/atomic package are lockless and usually implemented at the hardware level. 

So, if several concurrent processes are able to access a uint64 `foo`, using the atomic operation `atomic.AddUint64(&foo, 1)` is a thread-safe way to increment `foo`.

The package also provides atomic operations for values of arbitrary types, using `atomic.Value`. `atomic.Value` had two methods, `Load` and `Store`, both taking in an intnerface. However, once an `atomic.Value` is set to a specific type, subsequent `Store` calls will only accept values of that specific type. So if you create an `atomic.Value` and store a boolean in it like so: 

```go
foo := new(atomic.Value)
foo.Store(true)
```

then, `foo` cannot store a value of a type other than a boolean.


## TL;DR
When multiple process are accessing a value, it's important to make sure that that value can either be locked or operated on atomically.

#### Cites
* https://spin.atomicobject.com/2016/01/06/defining-atomic-object/
* https://gobyexample.com/atomic-counters

