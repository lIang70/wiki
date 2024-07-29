---
title: Keyword
description: 
published: true
date: 2024-07-29T13:33:36.374Z
tags: 
editor: markdown
dateCreated: 2024-07-29T13:33:36.374Z
---

# Keyword

## `static`

### Local Variable

- Ordinary local variables are stored in process stack space and are released immediately after use.

- Static local variable are stored in the global data area of the process and its value remains unchanged when the function returns. The compiler initializes them automatically.

### Global Variable

Static global variables are visible only to the ***current file*** and not accessible to other files, which can effectively reduce the coupling between program modules and avoid the conflict of variables with the same name of different files, and will not be misused.

### Object Oriented

#### Static Data Member

- `static` member variables enable information sharing between objects of the same class

- `static` members must be initialized outside the class. `static` cannot be added, and must follow the class name, i.e.

  ```c++
  <Data Type> <Class Name>::<Static Data Member> = <Value>;
  ```

- `static` members are stored outside the class and do not take up memory.

- It can be accessed either by class name or by object, i.e

  ```c++
  <Object>.<Static Data Member> or <Class Name>::<Static Data Member>
  ```

- `static` member is a global variable whose namespace belongs to a class and has been stored the `rw` segment of the data area.

#### Static Function Member

- A static member function does not have the `this` pointer. It cannot access non-static data members belonging to class objects, nor can it access non-static member functions. It can only call the remaining static member functions
- Function definitions that appear outside the class cannot specify the keyword `static`.
- Non-static member functions can arbitrarily access static member functions and static data members

## `typedef`

### Official Example

  ```c++
  typedef int P();
  typedef int Q();
  class X {
      static P(Q); // equal `static int Q()` or 'static P Q', Q isn't a type in this scope.
      static Q(P); // equal `static int Q(int ())`, define a function with Q.
  };
  ```

### Usages

- Define an alias for a type.

  ```c++
  typedef char* PCHAR;
  PCHAR pa, pb; // Is feasible. Pointers to two character variables are also declared
  ```

- The alias of struct.

  ```c++
  typedef struct tagPOINT {
      int x, y;
  } POINT;
  POINT p1;
  ```

- Define platform-independent types.

  ```c++
  typedef long double REAL; // Define a floating point type called REAL on target platform A
  typedef double REAL; // Change to double on target platform B that does not support long double
  typedef float REAL; // Change to float on target platform C, which does not support double
  
  // The standard library makes extensive use of this technique, such as size_t
  ```

- Define a new simple alias for a complex declaration.

  ```c++
  int *(*a[5])(int, char*); // The original statement
  //---------------------------------------------//
  typedef int *(*pFun)(int, char*);
  pFun a[5];
  ```

  ```c++
  void *(*b[10])(void(*)()); // The original statement
  //---------------------------------------------//
  typedef void (*pFunParam)();
  typedef void (*pFunx)(*pFunParam);
  pFunx b[10];
  ```

### Traps

- `typedef` is a new alias that defines a type that, unlike macros, is not a simple string substitution. Such as:

  ```c++
  typedef char* RSTR;
  int mystrcmp(const PSTR, const PSTR);
  // const PSTR -> char* const
  ```

  The reason is that `const` gives the entire pointer itself a constant character, forming the constant pointer `char* const`. When `const` and `typedef` are used together, `typedef` is not a simple string substitution.

- `typedef` is syntactically a key for a storage class (like `auto`, `extern`, `mutable`, `static`, `register`, etc.), although it does not really affect the storage properties of an object, such as:

  ```c++
  typedef static int INT2; // Is not workable
  ```

  The compilation will fail, prompting "More than one storage class was specified."

## `extern`

1. `extern` is optional in function declarations, because the function itself is `extern` without modification. But you need to declare it when you reference it.

2. The `extern` keyword is required when global variables are declared externally.

   > It also becomes the definition of a variable if the variable is not `extern` and is not explicitly initialized, so extern must be added, and the compiler loads and initializes this marker storage space when it executes.
   >
   > The declaration of a local variable cannot be `extern`, and local variables are allocated memory on the stack at run time.

3. There is essentially no difference between a global variable or a function.

   > the function name is a pointer to the beginning of the function binary block. A global variable is a variable declared outside of a function. The function name is also outside the function, so the function is also global.

4. The declaration can be multiple times, but the definition can only be once

   ```c++
   extern int i; // Declaration, not definition
   int i;        // It's a definition, it's a declaration
   ```

## `delete` and `delete[]`

### Basic Data Type

```c++
int *a = new int[10];
delete a;   // Method 1
delete[] a; // Method 2
```

Method 1 does not **leak memory**. For simple basic data types, Method 1 and Method 2 work properly, because:

> Basic datatype objects have no destructor, and when `new` allocates memory, it records the size of the allocated space, so `delete` will free memory correctly without calling the destructor to free the remaining Pointers.

### Custom Data Type

Suppose we get an array of objects via `new` and return a pointer. We need to do two things to free the array of objects:

- Release the portion of space originally requested.

- Call the destructor to do the cleanup.

For clearing memory space, as its size is recorded during application, either `delete` or `delete[]` can completely free this space. The problem is the call to the destructor. When `delete` is used, **only the destructor for the first object** in the array of objects is called, whereas with `delete[]` the destructor is called one by one.

## `new` and `malloc`

### Attribute

`new`/`delete` is a C++ keyword that requires compiler support. `malloc`/`free` is a library function that requires header file support.

### Parameter

When using the `new` operator to allocate memory, you do not need to **specify the size of the block**; the compiler calculates the size itself based on the type information. `malloc`, on the other hand, needs to explicitly indicate the size of memory required.

### Return Type

When the `new` operator allocates memory successfully, it returns a pointer to the object type. The type strictly matches the object and no type conversion is required. Therefore, `new` is a type-safe operator. `malloc` returns `void*`, which requires a cast to convert the `void*` pointer to the desired type.

### Allocation Failure

When memory allocation fails using the `new` keyword, the `bac_alloc` exception is raised. `malloc` returns `NULL` when memory allocation fails.

### Custom Type

- `new` calls the `operator new` function first, requesting sufficient memory (Usually the underlying implementation is `malloc`). It then calls the constructor of the type, initializes the member variable, and finally returns a pointer to the custom type. `delete` calls the destructor first, then the `operator delete` function to free memory (Usually the underlying implementation is `free`).

- `malloc`/`free` is a library function, can only dynamically apply and release memory, cannot force it to do custom type object construction and destruction work.

### Overloading

C++ allows overloading of the `new`/`delete` operators. In particular, the layout of `new` does not allocate memory for the object. Instead, it specifies an address as the starting area of memory on which `new` calls the constructor to initialize the object and returns the address. `malloc` does not allow overloading.

### Memory Area

The `new` operator dynamically allocates memory for objects from ***free storage***, while the `malloc` function dynamically allocates memory from the ***heap***.

> ***Free storage*** is an abstract concept based on the `new` operator in C++. Any memory that is allocated through the `new` operator is called ***free storage***.</br>
> ***Heap*** is a term in the operating system. It is a special piece of memory maintained by the operating system and used for dynamic memory allocation of programs. C language uses `malloc` to allocate memory from the ***heap*** and `free` to release allocated corresponding memory.

***Free storage*** is not equal to the ***heap***, and as mentioned above, layout `new` can be out of the ***heap***.

## `inline` VS `Macro`

- `inline` functions can be directly embedded in object code ***at compile time***.
- `inline` functions do ***parameter type checking***, which is an advantage of `inline` functions over macros.
- A macro definition cannot manipulate private members of a class, while an `inline` function can.
- If the code inside the function is long, using `inline` will result in high memory consumption.
- If there is a loop in the body of a function, it takes more time to execute the code in the body of the function than the cost of the function call.
