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

We can have only 8 threads then whats the purpose of creating `a new Thread()`?

- A core can run a thread when we create a `new Thread()` it gets added to the queue and will be
run in core when OS allocates it the needed core.

# We have only 8 threads and so where does the threads from thread pool come from ?

Spawning a thread is an costly operation, thus we have all the threads ready in the 
thread pool.

- The thread pool uses lazy creation of threads, its starts slowly and creates thread as per the usage
and need.

- We use `Task` to run a method using a thread from the thread pool.
- `Task` => Instruction set. (will explore more when in aynchronus programming section)




