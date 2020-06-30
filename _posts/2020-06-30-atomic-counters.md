---
layout:        post
title:         Atomic Counters
date:          2020-06-30 17:00:00
summary:       What are Atomic Counters?
categories:    blog
tags:          golang goroutines threads
---

# What are atomic counters?
Before talking about atomic counters, I want to define atomic operations and atomicity. An operation is atomic if at no point can the operation be divided -- it happens instantaneously and no other process can access the object that's being operated on in the midst of the operation (or in the process of changing it). In other words, atomic operations are individisble. There is only a before and after, and no in-between where the in-flux state can be accessed.

An atomic counter is _ .

## Why are they important?

Atomicity is important when dealing with concurrent operations that access and modify shared objects. To guarantee that the program works as intended, it's important to ensure that the shared objects can only be operated on atomically.

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

Another way to 

#### Cites
* https://spin.atomicobject.com/2016/01/06/defining-atomic-object/

