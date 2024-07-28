---
title: Object Model
description: 
published: true
date: 2024-07-28T14:41:51.709Z
tags: 
editor: markdown
dateCreated: 2024-07-28T14:21:03.426Z
---

# Object Model

## Class

> In `x32` machine. Object and pointer layouts for separate class.

```C++
class String {
    int len_ {};
    char* bytes_ { nullptr };
};

class ZooAnimal {
protected:
    int loc_ {};
    String name_ {};
};
```

![class-zoo-animal.png](/assets/cpp/class-zoo-animal.png)

> Adding *Polymorphism*

```c++
class Bear : public ZooAnimal {
public:
    Bear();
    ~Bear();
    
    void Rotate();
    
    virtual void Dance();

protected:
    enum Dances { ... };
    Dances  dances_known_;
    int     cell_block_;
}
```

![class-bear.png](/assets/cpp/class-bear.png)

### `sizeof()`

- Total size of *non-static member variables*.
- *alignment*.
- The overhead of *virtual*.

## Virtual Function

*Virtual Function*. The overridden member function is set as a virtual function in the base class.

> When the member function is called by a pointer or reference to the base class, the called function is determined based on **the type of the object to which the pointer points**, **not the type of the pointer**.

- You simply declare member functions in the base class as virtual functions, and overriding `virtual` functions in derived classes automatically become virtual functions.

- The destructor in the base class must be virtual, or an object release error will occur.

  > If the destructor for the base class is not declared `virtual`, then `delete` is called.  Statement calls the destructor of the base class instead of the derived class that should be called, causing the problem of object release errors.

- The use of the virtual function will make the objects of class take up more memory space.

  > Compiler add a implicit member to every object that contains a virtual function: a pointer to the virtual function table. **Virtual function table** contains the addresses of virtual functions shared by all virtual function objects. **When a derived class redefines a virtual function, the address of that function is added to the virtual function table**. No matter how many virtual functions are defined in a class object, there is only one pointer to the virtual function. Accordingly, each object is 4 bytes larger in memory than without virtual functions (32-bit host, excluding virtual destructors).

- **The characteristic identifier** (including the number, type and order of arguments) of the overriding function must be the same as that of the base class function, otherwise the base class function will be overwritten.

- Rewriting is different from overloading.

  > A function of the same name inside a class with different argument lists is called overloading; Rewriting is a "local modification" of a function with the same name as the base class by a derived class, requiring that the function features be exactly the same. Of course, the return value types are not necessarily the same

## Virtual Base Class

Virtual base classes allow objects derived from multiple classes that inherit from a single class to inherit from only one object

```c++
class Test: virtual public Base {
    
};
```

> `base` is the virtual base class of class `MyTest`. Assuming that `base` is also a virtual base class of another class `MyTest2`, for subclasses `MyTest3` that inherit from `MyTest` and `MyTest2`, portions of `base` are inherited only once.

```c++
class Base {
    int b;
public:
    virtual ~Base() {};
    virtual void Printf()
    {
        std::cout << "The base class method!" << std::endl; 
    }
};

class MyTest
    : virtual public Base {
    
};

class MyTest2
    : virtual public Base {
    
};

class MyTest3
    : public MyTest
    , public MyTest2 {
    
};

        +------+
     +--+ Base +--+ 
     |  +------+  |
     |            |
 +---V----+   +---V-----+
 | MyTest |   | MyTest2 |
 +---+----+   +------+--+
     |               | 
     |  +---------+  |
     +--> MyTest3 <--+
        +---------+      
```

- Why are `MyTest` and `MyTest2` the size of 12?

  > This is because they add a hidden member -- a pointer to the virtual base class, which takes up four bytes -- after the virtual inherits from the base class. The class `base` itself takes 8 bytes, so they are all 12. For non-virtual inheritance, such a pointer is not required. Class `MyTest3` has the following size:
  >

  $$
  sizeof(base)+sizeof(MyTest-base)+sizeof(MyTest2-base)=16
  $$

- When a class inherits multiple classes from a derived class with the same base class, ambiguities occur when a member function of the same name is called. To solve this problem, you can clarify it with the scope resolution operator `::`, or redefine it in the class.

- Inheritance can be very complicated.
  
  > A class may inherit from other classes, and its parent may inherit from other classes. When the class inherits two or more functions of the same name from different paths, not qualifying the class name to `virtual` results ***in ambiguity***. Of course, if virtual base classes are used, this does not necessarily lead to ambiguity.
  >
  > The compiler selects the "shortest" superclass member function on the inheritance path to call. This rule does not contradict the access control permissions of member functions. That is, you cannot call a lower-priority member function of the same name of type `public` because the higher-priority member function has `private` access control rights.

## Class's Construction and Destruction

1. **Which functions will be generated automatically by the null class compiler?**

   The compiler generates six member functions: a default constructor, a copy constructor, a destructor, an assignment operator, and two address operators.

   ```c++
   class Empty {
   public:
       Empty();
       Empty(const Empty &rhs);
       ~Empty();
       Empty& operator=(const Empty &rhs);
       Empty* operator&();
       const Empty* operator&() const;
   }
   ```

2. **Construction**
   > Base classes are constructed first, and subclasses are constructed later.

3. **Destruction**
   > The subclass is destructed first, then the base class.

## RTTI (*Run Time Type Identification*)

> However, due to polymorphism in object-oriented programming, the type of a pointer or Reference in C++ may not be the same as the type it actually represents.

### Implementation

- `typeid` - It used to return the type of the expression.

- `dynamic_cast<>` - Used to safely convert a pointer or reference to a base class into a pointer or reference to a derived class.

### *Notice*

- The `dynamic_cast<>` converter can only be used for classes that contain virtual functions.
- `RTTI`. The implementation requirement is that there must be virtual functions, that is, there must be a virtual function table. Without virtual tables, `RTTI` is meaningless!