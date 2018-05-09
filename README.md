# State of the Art of Parallel Javascript

Javascript is defined as a single-threaded programming and execution model, but
several independent designs and implementations for parallel Javascript exist
covering a spectrum of capabilities.
This list aims to capture the state of the art for parallel Javascript
and enabling technologies like shared-memory.

This is a work in progress, additions and corrections are welcome.
Please feel free to submit a PR to this list or send an update to *mogill@synsem.com*.


## Classification System 

This list separates parallelism and synchronization into separate concerns
to capture implementations ranging from shared memory with no source of
parallelism to shared-nothing multiprocessing.

- __Types of Parallelism__: How processes/threads are created and managed 
    - __Fork-Join__ - New threads/processes are created explicitly
    - __Bulk Synchronous Parallel__ - A fixed number of processes
      are started when the program is executed, all entering the program at `main()`.
    - __Loop Level__ - Iterations of loops are distributed to multiple processes/threads
    - __Asynchronous__ - Non-blocking function calls or continuation passing  
    - __None__ - No form of parallelism is provided by the platform
- __Types of Synchronization__: Categories of how processes enforce order of operations:
    - __Messaging__ - Active Messages/Remote Procedure Calls cause a function
      to execute using the message payload at the destination
    - __Atomic Read-Modify-Write__ - Fetch-And-Phi memory operations that atomically
      load from memory, perform an arithmetic or comparison operation, store the result back to memory,
      and return the original value stored in memory
    - __Barrier__ - Wait for a known number of processes to arrive before 
      any proceeds.  Frequently used with Bulk Synchronous Parallelism
    - __Mutex__ - Mutual exclusion locks and semaphores
    - __None__ - No synchronization is provided
- __Shared Memory__: Directly sharing memory between processes/threads 
    - __Sharing Model__: What is shared between threads/processes
        - __Shared Everything__ - All heap variables are shared unless explicitly made thread-private
        - __Explicit Sharing__ - Declaration of shared variables requires different syntax than private data
        - __Shared Nothing__ - No shared memory capabilities
    - __Coherency__: Whether or not all processes see the most recent copy of data
    - __Atomicity__: Whether or not other processes can see a partially completed memory operation



## What is Considered "Parallel Javascript" and "Shared Memory"
Because parallelism is not part of the base language, parallel JS might mean
anything from two mutually dependent Node.js programs communicating via sockets,
to transpiling a downloaded module before evaluating it,
to loop-level parallelism with mutex locks.

Accessing _shared memory_ differs from accessing shared remote caches, object stores, or databases
in that it is too fast to merit being made asynchronous.  Specifically, 
the overhead for scheduling a callback is greater than the
amount of time needed to perform the work.  Shared memory
also introduces the potential hazards when one process modifies the value
of a variable used by another thread.

Javascript projects do not use the terms *multithreaded* or *shared memory* consistently,
projects that describe themselves with those terms but might better be described
as *asynchronous* (i.e.: the `async` Node.js package) and/or *message passing* 
(i.e.: Node's built-in `cluster`) are not listed.

Shared memory can be implemented separately from a parallel programming model,
indeed the de-facto standard, Shared Array Buffers, exists solely to support parallel
programming but does not itself implement any parallelism, that is provided
by WebWorkers.  Parallel memory models
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

| Project Web Site                                                                                                                | Parallelism Model(s)                                        | Shared Memory Model                                                        | Unique Features                                                                                        | Intended Uses                                                                | Usage Examples                                               | Set # Procs | First Release | Latest Release | Active | Platform    |
|---------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|----------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|--------------------------------------------------------------|:-----------:|:-------------:|:--------------:|:------:|:-----------:|
| [AliOS                             ](https://github.com/alibaba/AliOS-nodejs/wiki/Workers-in-Node.js-based-on-multithreaded-V8) |  Fork-Join                                                  | N/A                                                                        | Proposal only, no implementation yet, work in progress                                                 | Asynchronous processing                                                      | N/A                                                          | ?           |           N/A |            N/A | Yes    | V8          |
| [Concurrent Javascript             ](https://webkit.org/blog/7846/concurrent-javascript-it-can-work/)                           |  Fork-Join                                                  | Shared everything, atomic Read-Modify-Write operations                     | C-like shared everything memory proposal, no implementation yet, work in progress                      | ?                                                                            | N/A                                                          | ?           |           N/A |            N/A | Yes    | WebKit      |
| [Extended Memory Semantics (EMS.js)](https://github.com/SyntheticSemantics/ems)                                                 |  Loop decomposition, Fork-Join, Bulk Synchronous Parallel   | Atomic Read-Modify-Write operations on persistent objects in shared memory | Allows any number or kind of processes and devices to share objects. Hardware accelerators supported.  | High Performance Computing, ad-hoc analytics on unstructured data.           | Parallel web server, word counting, transaction processing   | Yes         |          2013 |           2017 | Yes    | V8          |
| [Fibers                            ](https://github.com/laverdet/node-fibers)                                                   |  Fork-Join, Futures                                         | N/A                                                                        | Implements asynchronous functionality used by other asynchronous libraries                             | Asynchronous processing                                                      | Fibonacci                                                    | N/A         |          2011 |           2017 | Yes    | V8          |
| [isolated-vm                       ](https://github.com/laverdet/isolated-vm)                                                   |  Fork-Join                                                  | Limited sharing within an isolate, Shared arrays                           | Exposes v8 runtime API to JS, isolates created in JS run on separate threads                           | Sandboxed execution of user functions in an application                      | Quicksort                                                    | Yes         |          2017 |           2018 | Yes    | V8          |
| [JXcore                            ](https://github.com/jxcore/jxcore)                                                          |  Fork-Join                                                  | N/A                                                                        | Supported multiple engines (V8, Spider Monkey, Chakra), multi-threading, shared objects                | ?                                                                            | Fibonacci, messaging                                         | Yes         |          2014 |           2016 | No     | V8, SM, Chk |
| [mmap-object                       ](https://github.com/allenluce/mmap-object )                                                 |  N/A                                                        | Serially accessed shared object memory                                     | Native C++ implementation based on Boost                                                               | ?                                                                            | N/A                                                          | N/A         |          2017 |           2017 | Yes    | V8          |
| [Napa.js                           ](https://github.com/Microsoft/napajs)                                                       |  Fork-Join                                                  | Non-coherent shared objects                                                | Microsoft implemented for V8                                                                           | "...highly iterative services with non-compromised performance in Bing..."   | Fibonacci, Pi, Max square sub-matrix                         | Yes         |          2017 |           2017 | Yes    | V8          |
| [Nexus.js                          ](https://github.com/voodooattack/nexusjs)                                                   |  Asynchronous I/O                                           | N/A                                                                        | Makes possible promise-based multi-threading for WebKit                                                | Node.js-like asynchronous I/O for implementing web servers                   | Web server                                                   | No          |          2016 |           2017 | Yes    | JSC         |
| [node-multithread                  ](https://github.com/losfair/node-multithread)                                               |  Fork-Join                                                  | Non-coherent shared objects                                                | Map-reduce oriented                                                                                    | ?                                                                            | N/A                                                          | No          |          2017 |           2017 | No     | V8          |
| [node-shared-cache                 ](https://github.com/kyriosli/node-shared-cache)                                             |  N/A                                                        | Limited atomicity on shared objects                                        | BSON for internal storage Key-value store                                                              | N/A                                                                          | N/A                                                          | N/A         |          2015 |           2016 | No     | V8          |
| [node-worker-farm                  ](https://github.com/rvagg/node-worker-farm)                                                 |  Fork-Join                                                  | N/A                                                                        | Basic F-J running in a pool of separate Node.js processes                                              | Asynchronous processing                                                      | Pi                                                           | Yes (poolsz)|          2013 |           2017 | Yes    | Node.js     |
| [Parallel.js                       ](https://github.com/parallel-js/parallel.js)                                                |  Map-Reduce                                                 | N/A                                                                        | Web-worker based                                                                                       | Map-reduce                                                                   | N/A                                                          | No          |          2014 |           2016 | No     | V8          |
| [promise-worker                    ](https://github.com/nolanlawson/promise-worker)                                             |  Fork-Join                                                  | N/A                                                                        | Implements promises as workers                                                                         | Asynchronous processing                                                      | N/A                                                          | N/A         |          2017 |           2017 | Yes    | V8          |
| [RiverTrail                        ](https://github.com/IntelLabs/RiverTrail)                                                   |  SIMD, loop-level                                           | Shared arrays and global variables via closures(?)                         | Browser plug-in, OpenCL support, explicitly SIMD with implicit loop-level parallelism                  | Array & image processing, other OpenCL amenable algorithms                   | Image processing, OpenCL acceleration of JS                  | ?           |          2011 |           2017 | No     | SM          |
| [Shared Array Buffers              ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics)  |  N/A                                                        | Atomic Read-Modify-Write operations on a shared integer array              | De-facto standard supported by major JS implementations, C-like semantics                              | Target for Emscripten                                                        | N/A                                                          | N/A         |          2016 |           2017 | Yes    | V8, SM      |
| [WebWorkers                        ](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)                          |  Fork-Join                                                  | Messaging, shared array buffers                                            | De-facto standard supported in major browsers                                                          | Streaming media                                                              | N/A                                                          | Implicit    |          2010 |           2017 | Yes    | Chrome, FF  |
| [Workerpool                        ](https://github.com/josdejong/workerpool)                                                   |  Fork-Join                                                  | N/A                                                                        | Small implementation                                                                                   | Asynchronous processing                                                      | Background worker                                            | N/A         |          2014 |           2017 | Yes    | V8          |


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
