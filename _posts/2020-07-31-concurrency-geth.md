---
layout:        post
title:         Concurrency in Geth
date:          2020-07-31 17:08:00
summary:       Geth's concurrency model
categories:    blog
tags:          golang go-ethereum ethereum concurrency
---

# Concurrency in Geth

It's been a while since I made a post, so I thought it was about time. I wanted to do a longer write-up and I was recently chatting with Felix from the [go-ethereum](https://github.com/ethereum/go-ethereum) team about how a geth node handles [eth protocol](https://github.com/ethereum/devp2p/blob/master/caps/eth.md) requests from its peers. The answer to that, which I'll expand upon a bit later, is through an interesting concurrency model: one goroutine per peer.


