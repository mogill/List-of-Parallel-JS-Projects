# State of the Art of Parallel Javascript

Javascript is defined as a single-threaded programming and execution model, but
several independent designs and implementations for shared-memory parallel Javascript exist.

This list aims to capture the state of the art for parallel Javascript
and enabling technologies like shared-memory.
This is a work in progress, additions and corrections are welcome.
Please feel free to submit a
PR to this list or send an update to *mogill@synsem.com*.



## What is Considered "Parallel Javascript"

This list is limited to programming and execution models that implement 
lightweight threads or processes and expose them to the programmer through an API,
and/or implement a shared memory that does not require network communication with a
separate server process.  This list does not consider remote caches, object stores,
or databases as *shared memory*.
Reading and writing shared memory differ from communicating with a remote cache
or reading and writing a file in that they are synchronous operations that execute 
in less time than the overhead of scheduling a callback.


Javascript projects do not use the terms *multithreaded* or *shared memory* consistently,
projects that describe themselves with those terms but might better be described
as *asynchronous* and/or *message passing* are not listed.

Shared memory can be implemented separately from a parallel programming model,
indeed the de-facto standard, Shared Array Buffers, exists solely to support parallel
programming but does not itself implement any parallelism.  Parallel memory models
with no parallel execution model are included in this list because they are
complementary to other forms of parallelism.



## Concurrency vs. Parallelism

This document uses the terms *concurrent* and *parallel* according the definitions given
by the Go language:

> Concurrency is about dealing with lots of things at once.
> Parallelism is about doing lots of things at once.
> <br><br>
>         -GoBlog [Concurrency is Not Parallelism](https://blog.golang.org/concurrency-is-not-parallelism)


## List of Parallel and Shared-Memory Javascript Projects

| Project Web Site                                                                                                                | Parallelism Model(s)                                        | Shared Memory Model                                                        | Unique Features                                                                                        | Intended Uses                                                                | Usage Examples                                               | Set # Procs | First Release | Latest Release | Active | Engines     |
|---------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|----------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|--------------------------------------------------------------|:-----------:|:-------------:|:--------------:|:------:|:-----------:|
| [Concurrent Javascript             ](https://webkit.org/blog/7846/concurrent-javascript-it-can-work/)                           |  Fork-Join                                                  | Shared everything, atomic Read-Modify-Write operations                     | C-like shared everything memory proposal, no implementation yet, work in progress                      | ?                                                                            | N/A                                                          | ?           |           N/A |            N/A | Yes    | WebKit      |
| [Extended Memory Semantics (EMS.js)](https://github.com/SyntheticSemantics/ems)                                                 |  Loop decomposition, Fork-Join, Bulk Synchronous Parallel   | Atomic Read-Modify-Write operations on persistent objects in shared memory | Allows any number or kind of processes and devices to share objects. Hardware accelerators supported.  | High Performance Computing, ad-hoc analytics on unstructured data.           | Parallel web server, word counting, transaction processing   | Yes         |          2013 |           2017 | Yes    | V8          |
| [JXcore                            ](https://github.com/jxcore/jxcore)                                                          |  Fork-Join                                                  | Messaging                                                                  | Supported multiple engines (V8, Spider Monkey, Chakra), multi-threading, shared objects                | ?                                                                            | Fibonacci, messaging                                         | Yes         |          2014 |           2016 | No     | V8, SM, Chk |
| [mmap-object                       ](https://github.com/allenluce/mmap-object )                                                 |  N/A                                                        | Serially accessed shared object memory                                     | Native C++ implementation based on Boost                                                               | ?                                                                            | N/A                                                          | N/A         |          2017 |           2017 | Yes    | V8          |
| [Napa.js                           ](https://github.com/Microsoft/napajs)                                                       |  Fork-Join                                                  | Non-coherent shared objects                                                | Microsoft implemented for V8                                                                           | "...highly iterative services with non-compromised performance in Bing..."   | Fibonacci, Pi, Max square sub-matrix                         | Yes         |          2017 |           2017 | Yes    | V8          |
| [Nexus.js                          ](https://github.com/voodooattack/nexusjs)                                                   |  Asynchronous I/O                                           | N/A                                                                        | Makes possible promise-based multi-threading for WebKit                                                | Node.js-like asynchronous I/O for implementing web servers                   | Web server                                                   | No          |          2016 |           2017 | Yes    | WebKit      |
| [node-multithread                  ](https://github.com/losfair/node-multithread)                                               |  Fork-Join                                                  | Non-coherent shared objects                                                | Map-reduce oriented                                                                                    | ?                                                                            | N/A                                                          | No          |          2017 |           2017 | No     | V8          |
| [node-shared-cache                 ](https://github.com/kyriosli/node-shared-cache)                                             |  N/A                                                        | Limited atomicity on shared objects                                        | BSON for internal storage Key-value store                                                              | N/A                                                                          | N/A                                                          | N/A         |          2015 |           2016 | No     | V8          |
| [Parallel.js                       ](https://github.com/parallel-js/parallel.js)                                                |  Map-Reduce                                                 | N/A                                                                        | Web-worker based                                                                                       | Map-reduce                                                                   | N/A                                                          | No          |          2014 |           2016 | No     | V8          |
| [Shared Array Buffers              ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics)  |  N/A                                                        | Atomic Read-Modify-Write operations on a shared integer array              | De-facto standard supported by major JS implementations, C-like semantics                              | Target for Emscripten                                                        | N/A                                                          | N/A         |          2016 |           2017 | Yes    | V8, SM      |


- **Project Web Site** - Name of and link to the project
- **Parallelism Model** - Type(s) of parallelism the project exposes to applications
- **Shared Memory Model** - Coherency and communication of shared data
- **Unique Features** - Notable differentiators
- **Intended Uses** - Target application(s) of the project 
- **Examples** - Examples provided by implementation
- **Set # Procs** - Does the API allow the program to specify the number of parallel processes
- **First Release** - Date of initial public release
- **Latest Release** - Date of most recent release
- **Active** - Does the project have ongoing development in the past 6 months
- **Engines** - Supported Javascript engines

---
#### Revisions
- 2017-12-16 Initial list
