---
title: Smart Pointer
description: 
published: true
date: 2024-07-29T13:35:11.224Z
tags: 
editor: markdown
dateCreated: 2024-07-29T13:35:11.224Z
---

# Smart Pointer

- ***Smart Pointer*** was introduced to automatically call `delete` to release resources

- A smart pointer is not a pointer, but a ***class*** whose constructor takes the address of a ***dynamically created space*** (*The smart pointer is initialized with the pointer of the return value of `new` to achieve the purpose of resource initialization*).

- ***Smart Pointer*** = ***Pointer*** + ***RAII*** (*resource acquisition is initialization*), ***RAII*** wraps pointers into a class that manages pointers through constructors and destructors to prevent memory leaks (*A class automatically calls its destructor when it is out of scope*).

## `std::unique_ptr`

### *Anti-copy* (FEATURE)

> The limitations of `auto_ptr` are circumvent by limiting copy construction and assignment operations and only support transfer semantics

```c++
std::unique_ptr<int> ptr(new int(5));
std::unique_ptr<int> ptr1 = std::move(ptr); // After the transfer, the ptr is a null object, and the internal pointer is a null pointer
```

### *Provides the same interface as traditional pointers*

> But does ***not*** support ***pointer operations***.

### *Provides `release` method to release ownership*

> The difference between `release` and `reset` is that `release` ***only releases ownership but not resources***, and `reset` also releases resources.

## `std::shared_ptr`

### *Reference counting* (FEATURE)

### *Assignment/Copy support* (FEATURE)

### *Shared ownership* (FEATURE)

> `shared_ptr` records how many `shared_ptr` references the same object. The object is released only when the last smart pointer to the referenced object is destroyed.

### *Store smart pointers in standard containers*

> `shared_ptr` only raises the common reference count +1 for copy constructs and assignments. There is no problem that the arguments to copy constructs and assignments cannot be `const`).

### *Usage*

```c++
std::shared_ptr<int> ptr( new int );
```

- Use the `make_shared` macro to speed up the creation process.

  ```c++
  std::shared_ptr<int> ptr = std::make_shared<int>(100);
  ```

  - *Difference*
  
    > The default constructor allocates memory twice, whereas `make_shared` allocates memory only once, reducing the overhead of memory allocation.
  
  - Since `shared_ptr` contains ***a reference count*** and ***memory for the data***, it is said that there are ***two parts of memory***.
  
  - The default constructor allocates memory for the data and reference count separately, while `make_shared` ***allocates memory*** for the data and reference count ***together***.

  - *Problem*
  
    - `weak_ptr` also maintains the life cycle of the control block (*strong reference, weak reference information*), thus maintains the memory allocated by the object, which is released only when the last `weak_ptr` leaves scope.

    - Memory that can be ***freed*** when strong references are *reduced to 0*, but now it can be released only when ***both*** strong references and weak references are *reduced to 0*, unexpected ***delay memory release time***. This is a concern for memory-demanding scenarios.

### *Notice*

- Try not to create multiple `shared_ptr` from a bare pointer, because in this case, different `shared_ptr` objects will not know that they share pointers with other `shared_ptr` objects.

- ***Thread is not safe***, because the `ptr` will hang during assignment/copy.

## `std::weak_ptr`

### *Auxiliary `shared_ptr`* (FEATURE)

- Always through `shared_ptr` to initialize.

- `weak_ptr` does not support ordinary pointers including `*`, `->` operation.

- It does ***not contain resources***.

  > What actually owns the object memory is the `shared_ptr` object returned through the `lock()` interface.

- `weak_ptr` ***expires*** when `shared_ptr` ***goes out of scope***.

- Used to solve the problem of ***circular references***.

## `std::auto_ptr` (*Not recommended, Removed from C++17*)

### *Transfer of management right* (FEATURE)

> This is implemented by administrative transfer when the copy construct and assignment operators are overloaded.

### `auto_ptr` *cannot point to a set of objects*

> Which means it cannot be used with the `new[]` operator.</br>
> Because the destructor for `auto_ptr` is implemented using `delete` instead of `delete[]`. So when we create an array object, we get an error.

### `auto_ptr` *cannot be used with standard containers* (`vector`, `list`, `map`, etc.)

> When a standard container allocates memory, it must be able to copy the elements that construct the container. And when you copy a construct, you cannot change the value of the original element (Remember that all the elements in the copy constructor are `const T&`). The `auto_ptr` must change the value of the element when copying the construct.
