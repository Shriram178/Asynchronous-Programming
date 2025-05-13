# Asynchronous-Programming

# Hardware Understanding

A Processor's(CPU) number of cores determine how many applications it can run at a time.
CPU has units called cores which are the units that run an instruction set (task/apps).
By default 1 core has only one thread.

By using  Hyper Threading(Intel) or Simultaneous Multithreading(AMD)
we can hace 2 threads per core.

we can see this by going to task manager.

![TaskManager](https://github.com/user-attachments/assets/2bb2474e-0b50-47f2-bf12-61d24ee4b62a)

We can also see the graph displaying the usage of each thread.

# How does our  4-core laptop handle multiple tasks(email,browsing,Video streaming and image editing)?

This is achieved through `Scheduling` and `Time Slicing`.

When in the begining we only had one Core we will switch between multiple tasks to perfoem
operations or run applications, we are doing the same thing but for multiple cores instead of one.

This `time-slicing` and `scheduling` is handled and managed by the `OS`.

# Does OS take 1 thread out of the 8 inorder to run ?

Yes, the OS needs CPU time too, so now do we have only 7 threads left ?

Not exactly,

- Threads are not permanently reserved.
- the OS kernel like windows doesn't hog a core or thread.
- instead it shcedules itslef like anyother task.

### Interrupts and Priorities

- The OS and Hardware send interrupts that briefly take control of a CPU thread,
this handles quick tasks (e.g. keyboard inputs) and returns.

- OS tasks are high priority but usually lightweight and short lived.

![OS Multitasking](https://github.com/user-attachments/assets/d475e43c-040e-4d16-8631-518e01162b01)

For example :

- The OS acts like a manager who quickly steps in and organizes queue lines os 
solve samll issues,

- but the customers (app/task) are still served at all 8 counters.

- The manager doesn't permanently stand at one counter.

# Programming context

### We can have only 8 threads then whats the purpose of creating `a new Thread()`?

- A core can run a thread when we create a `new Thread()` it gets added to the queue and will be
run in core when OS allocates it the needed core.

- Think of a thread like an object which is created for executing stuff inside the core.

### We have only 8 threads and so where does the threads from thread pool come from ?

Spawning a thread is an costly operation, thus we have all the threads ready in the 
thread pool.

- We shold not create many objects often so we use threadpool which will have a collection of thread objects and borrow one for 
processing by updating the `execution context`.

![image](https://github.com/user-attachments/assets/c5dc0b37-32a1-4b2e-9ac3-0b1fab6eef0a)

- The thread pool uses lazy creation of threads, its starts slowly and creates thread as per the usage
and need.

- The `Thread pool` uses `factory pattern` to produce/reuse and provide us the Thread object.

- We use `Task` to run a method using a thread from the thread pool.

- `Task` => Instruction set. (will explore more when in aynchronus programming section)


# Asynchronous-Programming

The common misconception is that when a async program hits an await keyword
it allocates that awaited method to a another thread and moves on but it is not so.

In reality the `async` keyword is just a sytactic sugar for converting an funtion
into a state machine.

### State machine :

![image](https://github.com/user-attachments/assets/347f3ded-5c00-4146-81d6-7ebdae54ce17)

A state machine is an event driven system where we transition from one state to another based on certain conditions.

- When we use `async` behind the scenes the compiler transforms async method into a state machine to manage the asynchronus flow.

- Each `await` introduces a checkpoint where the method pauses and resumes after the awaited task completes.

The async function has 3 states 

![async state machine](https://github.com/user-attachments/assets/7d9c1692-ac29-476d-9348-f09395ac9f07)

### Async method execution

Since async is a state mission it can pause and resume execution.

- In the start the method executes synchronusly until it hits an `await`.
- At the await it checks the awaited task :

    - If the task is already `complete`, it continues synchronusly.
    - If the task is `not complete`,it returns control to the caller, and the rest of the method is paused
    - this awaited operation is a non-blocking one ( I/O, delay,file operation,etc).
    - when the awaited task completes then the continuation login is run.
- If `ConfigureAwait(true)` => resumes on original thread.
- If `ConfigureAwait(false)` => resumes on any available thread from the thread pool.

## How does the async method know when to continue ?

- This is where `Task` comes in.
- the awaited method returns a `Task` which notifies the state mission that the task is completed.
- By getting this Task the state machine will resume executing the continution logic/code.
- The returned task from the await and the continuation logic are concatenated as a single task and are returned.
- this returned task is used to notify the caller that the task is completed.

## So we await a method and it pauses ? if it pauses then where ist running ?

This depends on what we are awaiting.

### Awaiting an I/O bound task (e.g. Task.Delay, file operation, HTTP, etc)

- This does not run on any thread.
- - it runs via OS signals.
- It uses OS-level async mechanisms (like timers,sockets,etc)
- this is why thread is freed up during wait.

```csharp
await Task.Delay(1000);
```


### Awaiting a CPU-bound task (e.g. Task.Run())

- This runs on a thread pool thread.
- We explicitly offload work to a thread via `Task.Run()`.
- It runs on a background thread while we `await` it.

```csharp
// Runs on a thread from thread pool
await Task.Run(() => MyFunc());
```
