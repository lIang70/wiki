---
title: Multi-Threaded
description: 
published: true
date: 2024-07-29T13:39:05.302Z
tags: 
editor: markdown
dateCreated: 2024-07-29T13:39:05.302Z
---

# Multi-Threaded

> ***Thread safety*** is a computer programming concept applicable to multi-threaded code. ***Thread-safe*** code only manipulates shared data structures in a manner that ensures that all threads behave properly and fulfill their design specifications without unintended interaction. There are various strategies for making thread-safe data structures.

## `std::thread`

### *Main function*

- `join()`
- `detach()`
- `get_id()`
- `native_handle()` is used for thread control of system.

### *namespace* `std::this_thread`

- `yield()`: This is usually used when your primary task has been completed and you want to ***free up the processor*** for other tasks.
- `get_id()`: Returns the ID of the current thread, which can be used to identify different threads.
- `sleep_for()` / `sleep_until()`: Causes execution of the current thread to ***stop*** for *a specified period of time*.

## `std::mutex`

- Provide basic mutual exclusion facilities.
- `std::recursive_mutex` provides mutually exclusive facilities that can be recursively locked by the ***same thread***.
- `std::timed_mutex` / `std::recursive_timed_mutex`  provides mutual exclusion facilities with ***timeout*** capabilities.

### `std::recursive_mutex`

> recurse mutex, it can be reentrant.

### `std::timed_mutex`

> mutex with timeout.

### `std::recursive_timed_mutex`

### `RAII`*(Resource Acquisition Is Initialization)*

- `std::lock_guard` && `std::unique_lock`
- *Locking Strategies*
  - `std::defer_lock` - No exclusive ownership is acquired.
  - `std::try_to_lock` - Try to gain ownership of mutual exclusion without blocking.
  - `std::adopt_lock` - Assume that the caller already owns the mutex.

## `std::condition_variable`

### `wait()`

- It can be broken down into ***three operations*** in turn:
  - Release the mutex.
  - Wait on the condition variable.
  - Acquire the mutex again.

## `std::atomic`

### *Difference between* `volatile`

- `std::atomic` is used to access data in multi-thread env, and without mutex, usually used concurrent programming.

- `volatile` is used to read/write memory which cannot be optimized, usually used in special memory. It cannot solve problem *Thread Safety*.

## `std::call_once`

> Ensure that a function is called only once in a multithreaded environment.

## `std::future`

- Obtain the task execution status, the `wait_for` method can be used when we do not need to wait indefinitely, but only for a certain amount of time.

  ```c++
  std::future<int> result = std::async(func, 255);
  std::future_status status = result.wait_for(1s); // wait one second also use wait_for(0s) to get status
  if (status == std::future_status::timeout)
      std::cout << "time out" << std::endl;
  else if (status == std::future_status::ready)
      std::cout << "mission finish" << std::endl;
  else if (status == std::future_status::deferred)
      std::cout << "synchronization task" << std::endl;
  ```

## `std::async`

### `std::launch` *enumeration*

```c++
// enum class launch
LIBCPP_DECLARE_STRONG_ENUM(launch)
{
    async = 1,
    deferred = 2,
    any = async | deferred
};
```

- `std::async`: The asynchronous task must create a new thread to execute the task delivered by the user. If the new thread cannot be created due to resource shortage, the task is terminated and an exception is thrown
- `std::deferred`: The asynchronous task necessarily does not create a new thread to execute the user-passed task, and a deferred call occurs, meaning the task is executed when the user calls `get()`. Also, if the user does not call the `get()`method, the asynchronous task will not be executed.

```c++
std::future<int> result = std::async(std::launch::async, func, para...);
std::future<int> result = std::async(std::launch::deferred, func, para..);
  ```

## `std::packaged_task`
