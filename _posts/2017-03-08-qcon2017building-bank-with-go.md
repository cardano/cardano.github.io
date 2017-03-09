---
layout: post
title: Building a Bank with Go
date: 2017-03-08
author: a.johnston
categories: update cardano go qcon matt-heath monzo
description: Notes from presentation by Matt Heath from Monzo at QCon London 2017.  He talks about choosing Go for it's concurrency model which is suited to low latency distributed systems.
---

# Summary
[Go](https://golang.org/) was mentioned many times during this conference, especially in the context of microservices.  It's light weight, opinionated, easy concurrency, small size and many other features are making it one of the languages of choice for cloud services. Matt's excellent presentation was standing room only which shows the interest.  He talks about why they chose Go, a pretty new platform for something as critical as banking.

Whilst I think it is too early for us to switch to it just yet, it is a language to keep an eye on.

The full presentation from Matt is available on the [QCon Site](https://qconlondon.com/london-2017/presentation/building-a-bank-with-Go).

# Key Takeaways
These are my bullet points from Matt's presentation.
* Memory managed
* Statically typed
* Excellent concurrency
* Perfect for simple, small, network services.
* Go services are like 20mb compared to several hundred for jvm.
* Lightweight concurrency
* [Goroutines](phttps://tour.golang.org/concurrency/1]).  Functions that runtime manages across multiple threads.
* Can run hundreds of thousands of functions easily.
* Each thread is around 8k rather than 1mb like jvm.
* Channels, pipeline between functions to pass data.
* Simplicity.  Small syntax so only one way to do tings. 
* Server in 2 lines of code.  Sensible defaults.
* Executable is just one binary.
* Go-kit. Deployment framework.
* Micro.  Microservices framework.
* Monzo/typhon.  There open framework.
* They use [linkerd](https://linkerd.io).  Provides RPC between process.  Load balance, service, plugs into [Kubernetes](https://kubernetes.io/).
* Uses reverse proxy to forward messages.
* They use docker and Kubernetes.
* Reduced price by a third by switching to Kubernetes from dedicated infra.
* [Monzo](https://github.com/monzo) open sourced [Phosphor](https://github.com/monzo/phosphor), a Distributed Tracing system, similar to [Google's Dapper paper](https://research.google.com/pubs/pub36356.html), Twitter's [Zipkin](http://zipkin.io/), and [Hailo's Trace Service](https://speakerdeck.com/mattheath/scaling-microservices-in-go-high-load-strategy-2015?slide=45). 
* Very opinionated which he thinks makes it easier to use.
* Simple, easy to get a team productive.

For full details see [Matt Heaths excellent presentation](https://qconlondon.com/london-2017/presentation/building-a-bank-with-Go).
