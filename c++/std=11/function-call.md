---
title: Function Call
description: 
published: true
date: 2024-07-29T13:39:56.277Z
tags: 
editor: markdown
dateCreated: 2024-07-29T13:39:56.277Z
---

# Function Call

## `std::function`

### *Callable Object*

- Function pointer.
- Class objects (*legendary functor*) with operator() member functions.
- `lambda` expression.
- A class object that can be converted to a function pointer.
- Class member (*function*) pointer.
- `std::bind` expression or other function object.

### *About* `std::function`

- An instance of std::function can store, copy, and call any *callable object*.
- The stored callable object is called the target of std::function. If std::function does not contain a target, it is called null.
- Targets that call an empty `std::function` throw a `std::bad_function_call` exception.

### *Example for* `std::function`

```c++
#include <functional>
#include <iostream>

class Foo {
public:
    int num_;

    Foo(int num) : num_(num) {}
    
    void PrintAdd(int i) const { 
        std::cout << num_ + i << '\n'; 
    }
};

void PrintNum(int i)
{ 
    std::cout << i << '\n'; 
}

class PrintNum {
public:
    void perator()(int i) const
    {
        std::cout << i << '\n';
    }
};

int main() {
    // Stored free function.
    std::function<void(int)> f_display = printNum;
    f_display(-9);

    // Stored lambda.
    std::function<void()> f_display_42 = []() { printNum(42); };
    f_display_42();

    // Stored the result of the std::bind call.
    std::function<void()> f_display_345256 = std::bind(printNum, 345256);
    f_display_345256();

    // Stored to member functions.
    std::function<void(const Foo&, int)> f_add_display1 = &Foo::print_add;
    const Foo foo(234);
    f_add_display1(foo, 1);
    f_add_display1(234, 1);

    // Stored to data member accessors.
    std::function<int(Foo const&)> f_num = &Foo::num_;
    std::cout << "num_: " << f_num(foo) << '\n';

    // Stored to member function and object
    std::function<void(int)> f_add_display2 = std::bind(&Foo::print_add, foo, std::placeholders::_1);
    f_add_display2(2);

    // Stored to member function and object pointer
    std::function<void(int)> f_add_display3 = std::bind(&Foo::print_add, &foo, std::placeholders::_1);
    f_add_display3(3);

    // Stored to function object
    std::function<void(int)> f_display_obj = PrintNum();
    f_display_obj(18);
}
```

## `std::bind`

- Bind the callable object with arguments for another `std::function` for invocation.
- Turn an n-element callable into an m-element(*m < n*) object and bind some parameters with using `std::placeholder`.

## `lambda`

> Lambda expressions in programming are usually used when a function is needed but you don't want to bother naming it, i.e. anonymous functions.

### *Template*

```c++
[capture list] (params list) mutable -> return type { function body }
```

- `mutable` indicator: used to say whether a captured variable can be ***modified*** (can be omitted)

### *The Specific Format*

| index |                           format                           |
| :---: | :--------------------------------------------------------: |
|   1   | `[capture list](params list) ->return type{function body}` |
|   2   |        `[capture list](params list){function body}`        |
|   3   |              `[capture list]{function body}`               |

### *Example*

```c++
std::vector<int> myvec{3, 2, 5, 7, 3, 2};
std::vector<int> lbvec(myvec);
sort(myvec.begin(), myvec.end(), cmp); 
sort(lbvec.begin(), lbvec.end(), [](int a, int b)->bool { return a < b; });
```

### *Capturing External Variables*

1. Value capture

   ```c++
   int a = 123;
   auto f = [a] { std:cout << a << std::endl; };
   a = 321;
   f();
   ```

2. Refer to capture

   ```c++
   int a = 123;
   auto f = [&a] { std:cout << a << std::endl; };
   a = 321;
   f();

3. Implicit capture (*lets the compiler infer which variables need to be captured based on the code in the function body*)

   ```c++
   int a = 123;
   auto f = [=] { std:cout << a << std::endl; };
   f();
   ```

   ```c++
   int a = 123;
   auto f = [&] { std:cout << a << std::endl; };
   a = 321;
   f();
   ```

4. Mixed type

   ```c++
   []                      // No external variables are captured。
   [(variable name), ...]  // By default, the specified number of external variables are captured as values. If capturing is applied, the declaration needs to be displayed
   [this]                  // Captures the this pointer as a value
   [=]                     // Capture all external variables as values
   [&]                     // Capture all external variables as references
   [=, &x]                 // The variable x is captured as a reference, and the remaining variables are captured as values
   [&, x]                  // The variable x is captured as a value, and the remaining variables are captured as references
   ```

### *Notice*

> - You ***cannot*** have ***default parameters*** in the parameter list.
> - ***Variable parameters*** are ***not*** supported.
> - All parameters ***must*** have ***parameter names***.
