# Async Overview

A quick overview of asynchronous IO on the JVM

---

# The problem

Your program can typically be

## CPU-bound

The bottleneck is your CPU. You can usually scale using a faster CPU, or more CPUs (if the tasks can be divided into smaller subtasks). 

* Example: running Monte-Carlo simulations.

## IO-bound

The bottleneck is your IO performance. There are a lot of concurrent requests, or the network traffic is very high. Scaling the CPU does not help.

* Example: web servers (high concurrent output), streaming servers (high sequential output).

If your problem is both CPU *and* IO intensive, you can divide it into smaller parts where each part is only CPU *or* IO intensive.

---

# Blocking IO

The naive solution is to use blocking IO, where each *read* or *write* blocks the CPU for the active process or thread while it is waiting for the results. Historically in Unix this was the foundation upon which *pipes* were built. It has some nice properties:

* Blocking IO is easy to program, the flow is sequential, trivial to follow what happens when.
* It has flow-control built in: when the producer writes and the consumer is not ready to read, the producer will block, resulting in back-pressure.

It also has downsides:

* To handle multiple requests concurrently, each request must be in its own process or thread. Processes are expensive, threads are also expensive.
* Handling parallel requests is also expensive: if you make a blocking IO call you have to wait for its results until you can make the next call.
