---
title: Singleton
description: 
published: true
date: 2024-07-29T13:42:47.064Z
tags: 
editor: markdown
dateCreated: 2024-07-29T13:42:47.064Z
---

# Pattern [*Singleton*]

## *Overview*

```bash
# UML
            +-------
            |
+-----------+-------------+      |
| Singleton               |      |
+-------------------------+      |
|-instance:Singleton      +>-----+
+-------------------------+
|-Singleton()             |
|+getInstance():Singleton |
+-------------------------+
```

## *Demo*

```c++
#ifndef __SINGLETON_H__
#define __SINGLETON_H__

#include <iostream>
#include <string.h>
#include <mutex>

class Singleton {
public:
 static Singleton* getInstance() {
  if (instance == NULL) {
   m_mutex.lock();
   if (instance == NULL) {    // DCLP
    instance = new Singleton();
   }
   m_mutex.unlock();
  }
  return instance;
 }
    
private:
 Singleton(){}

 static Singleton* instance;
 static std::mutex m_mutex;
};

Singleton* Singleton::instance = nullptr;
std::mutex Singleton::m_mutex;

#endif // __SINGLETON_H__
```

## *Q&A*

1. Why is the singleton pattern not thread-safe?

   > Discover and exploit parallelism in serial code.
   >
   > `DCLP` tries to use only one language for its purposes, so `DCLP` is unreliable.

2. How does `DCLP` deal with this problem?

   ```c++
   instance = new Singleton();
   ->
       1. instace =        -- step 3 assignment
       2. operator new (sizeof(Singleton))  -- step 1 open up memory
       3. new (instance) Singleton;   -- step 2 construct object
   ```

3. Why `DCLP` may fail in both uniprocessor and multiprocessor architectures?

4. A simple solution to this problem.

   ```c++
   Singleton* Singleton::m_instance;
   Singleton* Singleton::getInstance() {
       static std::once_flag oc;
       std::call_once(oc, [&] { m_instance = new Singleton(); });
       return m_instance;
   }
   ```

   *or*

   ```c++
   static Singleton* getInstance() {
       static Singleton singleton;  // The double-checked locking issue was fixed in C++11
       return &singleton;
   }
   ```
