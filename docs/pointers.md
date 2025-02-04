  * [Raw pointer](#raw-pointer)
    + [Dereference a Pointer](#dereference-a-pointer)
    + [Null pointers](#null-pointers)
    + [NULL vs nullptr](#null-vs-nullptr)
    + [Wild pointer](#wild-pointer)
    + [Dangling pointer](#dangling-pointer)
  * [Memory safety and Pointers](#memory-safety-and-pointers)
- [Smart Pointers](#smart-pointers)
  * [unique_ptr](#unique-ptr)
    + [Releasing a unique pointer](#releasing-a-unique-pointer)
    + [Returning a unique pointer](#returning-a-unique-pointer)
  * [shared_ptr](#shared-ptr)
    + [Avoiding cyclic references when using shared pointers](#avoiding-cyclic-references-when-using-shared-pointers)
  * [weak_pointer](#weak-pointer)
  * [Checking for null in smart pointers](#checking-for-null-in-smart-pointers)
  * [Pointer casting](#pointer-casting)
  * [Passing smart pointers to functions](#passing-smart-pointers-to-functions)
    + [Pass by value to lend the ownership](#pass-by-value-to-lend-the-ownership)
    + [Passing by reference to manipulate the ownership](#passing-by-reference-to-manipulate-the-ownership)
    + [Passing simple raw pointers/references](#passing-simple-raw-pointers-references)
  * [Return smart pointers from functions](#return-smart-pointers-from-functions)
  * [Reference](#reference)
  * [lvalues references and rvalues references](#lvalues-references-and-rvalues-references)
  * [reference wrapper](#reference-wrapper)
  * [removing reference/ pointer](#removing-reference--pointer)
  * [Atomic Smart Pointers](#atomic-smart-pointers)

## Raw pointer

 The size of a pointer is dependent upon the architecture of the computer — a 32-bit computer uses 32-bit memory addresses — consequently, 
a pointer on a 32-bit machine is 32 bits (4 bytes). On a 64-bit machine, a pointer  would be 64 bits (8 bytes).
This is true regardless of what is being pointed to:

```cpp
std::cout<<"sizeof(char *)= " << sizeof(char *)<<std::endl;
std::cout<<"sizeof(int *)= " <<sizeof(int *)<<std::endl;
std::cout<<"sizeof(void *)= " <<sizeof(void *)<<std::endl;
std::cout<<"sizeof(double *)= " <<sizeof(double *)<<std::endl;
std::cout<<"sizeof(person *)= " <<sizeof(person *)<<std::endl;
std::cout<<"sizeof(employee *)= " <<sizeof(employee *)<<std::endl;
```
### Dereference a Pointer

When you want to access the data/value in the memory that the pointer points to - the contents of the address with that numerical index - then you **dereference** the pointer.
Consider given a pointer such as p below:
```cpp
const char* p = "abc";
```
To refer to the characters p points to, we dereference p using one of these notations:
```cpp
*p == 'a';  // The first character at address p will be 'a'
p[1] == 'b'; // p[1] actually dereferences a pointer created by adding  p and 1 times the size of the things to which p points,  In this case they're char which are 1 byte in C...
*(p + 1) == 'b';  // Another notation for p[1]
```

### Null pointers

In **C**, `NULL` and `0` - and additionally  in **C++** `0x` or `NULL` was replaced by `nullptr` 
which can be used to indicate that a pointer doesn't currently hold the memory address of a variable, and shouldn't be dereferenced. 

### NULL vs nullptr
`0` or `NULL` could cause ambiguity in overloaded function resolution,for instance:
```cpp
f(int);
f(foo *);
```
you can `0` to both of them. in C++ `nullptr` has type `std::nullptr_t` and it's implicitly convertible to any pointer type. Thus, it'll match `std::nullptr_t` or pointer types in overload resolution, but not other types such as int.


### Wild pointer

wild pointer is an uninitialized pointers. It points to some random address in the memory and may cause a program to crash or behave badly.

```cpp
    // wild pointer, uninitialized, pointing to some unknown memory location 
    int *ptr;  
    //   This will cause the program to crash or behave badly. 
    *ptr = 10; 
```  
to avoid this, allway define the pointer as following:


```cpp
      int *ptr=nullptr; 
```  


### Dangling pointer
A dangling pointer is a (non-NULL) pointer which points to unallocated (already freed) memory area.
```cpp
Class *object = new Class();
Class *object2 = object;

delete object;
object = nullptr;
// now object2 points to something which is not valid anymore
```

This can occur even in stack allocated objects:

```cpp
Object *method() {
  Object object;
  return &object;
}

Object *object2 = method();
// obj
```
Refs: [1](https://stackoverflow.com/questions/17997228/what-is-a-dangling-pointer)

## Memory safety and Pointers
The following are some typical causes of a segmentation fault:
1) Attempting to access a nonexistent memory address (outside process's address space)
2) Attempting to access memory the program does not have rights to (such as kernel structures in process context)
3) Attempting to write read-only memory (such as code segment)
These often happens while dereferencing or assigning null pointer/ wild pointer/ dangling pointer, heap overflow, stack overflow
When you access an array index, C and C++ don't do bound checking. Segmentation faults only happen when you try to
read or write to a page that was not allocated (or try to do something on a page which isn't permitted,
e.g. trying to write to a read-only page), but since pages are usually pretty big
(multiples of a few kilobytes), it often leaves you with lots of room to overflow.


By setting the followings flag you can find the issue:
```
set(CMAKE_CXX_FLAGS "-fsanitize=address ${CMAKE_CXX_FLAGS}")
set(CMAKE_CXX_FLAGS "-fno-omit-frame-pointer ${CMAKE_CXX_FLAGS}")
```

# Smart Pointers
Smart pointers represent a way to express ownership over a resource. They prevent memory leaksing by making the memory deallocation automatic and making object destruction automatic. An object controlled by a smart pointer is automatically destroyed when the last (or only) owner of an object is destroyed.
An object can solely own another object (unique_ptr), or it can share the ownership of an object with others (shared_ptr), or it does not own but only refers to an object(weak_pointer). 


## unique_ptr
Sole ownership: 
Use cases of sole ownership include having to move around an expensive to copy resource, a resource that is owned by a single subroutine but needs to be dynamically allocated, passing a resource between threads, various implementations of singletons or pimpl and compatibility with C libraries which use pointers.

Raw pointers do not establish the unique ownership themselves. A class that uses raw pointers for unique ownership must explicitly implement the move-construction/assignment and disallow copy-construction/assignment. 

unique pointer, has less overhead relative to share_ptr, so **use a unique pointer instead of a 
shared pointer** where it suffices. A unique pointer has no copy constructor can not be copied, and can only be moved. Also possible to transfer the exclusive ownership by moving. The underlying resource is destructed when it exists the scope.



There are different ways to create a unique pointer:

First way:
```cpp
std::unique_ptr<person> entity=std::unique_ptr<person>(new person);
```

Second way(recommended) for exception safety
```cpp
std::unique_ptr<person> entity=std::make_unique<person>();
```

This will fail because it is a unique pointer, it can not be copied:
```cpp
std::unique_ptr<person> secondentity=entity;
```

This will work because because we have move constructor
```cpp
std::unique_ptr<person> secondentity=std::move(entity);
```


### Releasing a unique pointer

`release()` method releases the ownership of its stored pointer, return its value and replace it with a null pointer. This call will not destroy the managed object, but the unique_ptr object is released from the responsibility of deleting the object. you have to delete the object at some point.
To force the destruction of the object pointed, either use member function reset or perform an assignment operation on it.

```cpp
std::unique_ptr<int> unique_pointer (new int);
int * manual_pointer;

*unique_pointer=1;
manual_pointer = unique_pointer.release();

if(unique_pointer ==nullptr )
  std::cout << "unique_pointer is now empty" << '\n';

std::cout << "manual_pointer points to " << *manual_pointer << '\n';

delete manual_pointer;
```
Refs: [1](https://www.cplusplus.com/reference/memory/unique_ptr/release/)




### Returning a unique pointer


Refs: [1](https://stackoverflow.com/questions/4316727/returning-unique-ptr-from-functions)



## shared_ptr
Shared ownership: shared pointer, It is  mainly meant for multi-threaded resource sharing and gives the guarantee that the object won’t be freed by another thread. The managed object is deleted when the last owning shared_ptr is destroyed. In a typical implementation, a shared_ptr contains only two pointers: 
1. A raw pointer to the managed object (can be returned by calling get()) 
2. A pointer to the control block.

Several shared_ptr instances can share the management of an object's lifetime through a common **control block**. 


```
|----------------------------|                                                |----------------------------|
|   shared_ptr<>Ptr1         |                                                |   shared_ptr<>Ptr1         |
|----------------------------|                   |------------|               |----------------------------|
|Pointer to the Object       |------------------>|   Object   |<--------------|Pointer to the Object       |
|                            |                   |------------|               |                            |   
|Pointer to the control block|----|                                       |---|Pointer to the control block|
|----------------------------|    |                                       |   |----------------------------|
                                  |                                       |
                                  |                                       |
                                  |                                       |   
                                  |    |-----------------------------|    |
                                  |    |       control block         |    |
                                  |--->|raw pointer to object        |<---| 
                                       |Shared reference counter     |
                                       |Weak reference counter       |
                                       |Pointer to allocator, deleter|
                                       |-----------------------------|
```


Refs: [1](https://www.nextptr.com/tutorial/ta1358374985/shared_ptr-basics-and-internals-with-examples)




There are several ways to create a shared pointer:

Single line with new:
```cpp
std::shared_ptr<manager> ceo=std::shared_ptr<manager>(new manager);
std::shared_ptr<manager> ceo1(new manager);
```

Multi line:
```cpp
std::shared_ptr<manager> cto;
cto.reset(new manager);
```

Preferred way using make_shared:
```cpp
std::shared_ptr<manager> boss=std::make_shared<manager>();
```
When you create an object with new , and then create a shared_ptr , there are two dynamic memory allocations that happen: 
one for the object itself from the new, and then a second for the manager object created by the shared_ptr constructor.
 when you use make_shared, C++ compiler does a single memory allocation big enough to hold both the manager object and the new object.

when  you erase items in a vector the occupied space will be freed, but if you have stored pointers inside a vector you have to manualy call delete 
function to free the space.

The destructor for `std::vector<T>` ensures that the destructor for T is called for every element stored in the vector.


### Avoiding cyclic references when using shared pointers 

Refs: [1](https://stackoverflow.com/questions/12030650/when-is-stdweak-ptr-useful#:~:text=The%20example%20illustrates%20how%20a,(a.k.a.%20a%20circular%20reference).)

## weak_pointer
std::weak_ptr is a very good way to solve the **dangling pointer** problem. By just using raw pointers it is impossible to know if the referenced 
data has been deallocated or not. Instead, by letting a `std::shared_ptr` manage the data, and supplying `std::weak_ptr` to users of the data, the users can check validity of the data by calling `expired()` or `lock()`.

You could not do this with `std::shared_ptr` alone, because all `std::shared_ptr` instances share the ownership of the data which is not removed before all instances of `std::shared_ptr` are removed. Here is an example of how to check for dangling pointer using `lock()`:


```cpp
   // OLD, problem with dangling pointer
    // PROBLEM: ref will point to undefined data!

    int* ptr = new int(10);
    int* ref = ptr;
    delete ptr;

    // NEW
    // SOLUTION: check expired() or lock() to determine if pointer is valid

    // empty definition
    std::shared_ptr<int> sptr;

    // takes ownership of pointer
    sptr.reset(new int);
    *sptr = 10;

    // get pointer to data without taking ownership
    std::weak_ptr<int> weak1 = sptr;

    // deletes managed object, acquires new pointer
    sptr.reset(new int);
    *sptr = 5;

    // get pointer to new data without taking ownership
    std::weak_ptr<int> weak2 = sptr;

    // weak1 is expired!
    if(auto tmp = weak1.lock())
        std::cout << *tmp << '\n';
    else
        std::cout << "weak1 is expired\n";
    
    // weak2 points to new data (5)
    if(auto tmp = weak2.lock())
        std::cout << *tmp << '\n';
    else
        std::cout << "weak2 is expired\n";
```

Weak pointer, they don't increase the ref count:

```cpp
std::shared_ptr<person> person0;
{
    std::shared_ptr<person> sharedEntity0(new person(0));
    person0=sharedEntity0;
}
std::cout<<"sharedEntity0 is still alive because of person0=sharedEntity0" <<std::endl;
```

if we change the above code to the following the object will die after the {} block

```cpp
std::weak_ptr<person> person1;
{
    std::shared_ptr<person> sharedEntity1(new person(1));
    person1=sharedEntity1;
}
std::cout<<"sharedEntity1 died befor reaching here since person1 is a weak_ptr" <<std::endl;
```



## Checking for null in smart pointers

```cpp
ptr != nullptr 
```
or 

```cpp
!ptr
```


## Pointer casting

```cpp
std::shared_ptr<Base> b(new Base());
std::shared_ptr<Derived> d = std::static_pointer_cast<Derived>(b);

std::shared_ptr<person> b(new person());
std::shared_ptr<manager> d = std::static_pointer_cast<manager>(b);

```



## Passing smart pointers to functions

According to the [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#r30-take-smart-pointers-as-parameters-only-to-explicitly-express-lifetime-semantics) a function should take a smart pointer as parameter only if it examines/manipulates the smart pointer itself, i.e:
counting the references of a std::shared_ptr, increasing them by making a copy, change of ownership (moving data from a std::unique_ptr to another one).

Smart pointers can be passed to functions in following ways
```cpp
void f(std::unique_ptr<Object>);    // (1)
void f(std::shared_ptr<Object>);    // (2)
void f(std::weak_ptr<Object>);      // (3)
void f(std::unique_ptr<Object>&);   // (4)
void f(std::shared_ptr<Object>&);   // (5) also const &
void f(Object&);                    // (6) also const &
void f(Object*);                    // (7) also const *
```


### Pass by value to lend the ownership
Cases in (1), (2), (3), Pass smart pointers by value to lend their ownership to the function, that is when the function wants its own copy of 
the smart pointer in order to operate on it.


1) A std::unique_ptr can't be passed by value because it can't be copied, so it is usually moved around with the special function std::move from the Standard Library. This is move semantics in action:

```cpp
std::unique_ptr<Object> up = std::make_unique<Object>();
function(std::move(up)); // Usage of (1)
```
After the call `up` is a hollow object. This is known as a sink: the ownership of the dynamically-allocated resource flows down an imaginary sink from one point to another;

2) There's no need to move anything with std::shared_ptr: it can be passed by value (i.e. can be copied). Just remember that its reference count increases when you do it


3) std::weak_ptr can be passed by value as well. Do it when the function needs to create a new std::shared_ptr out of it, which would increase the reference count:
```cpp
void f(std::weak_ptr<Object> wp)
{
  if (std::shared_ptr<Object> sp = wp.lock())
      sp->doSomething();
}
```

### Passing by reference to manipulate the ownership

Pass by reference when the function is supposed to modify the ownership of existing smart pointers. More specifically:
1) pass a non-const reference to `std::unique_ptr` if the function might modify it, e.g. delete it, make it refer to a different object and so on.

2) the same applies to std::shared_ptr, but you can pass a const reference if the function will only read from it (e.g. get the number of references) 
or it will make a local copy out of it and share ownership.

### Passing simple raw pointers/references

Go with a simpler raw pointer (can be null) or a reference (can't be null) when your function just needs to inspect the underlying object or do something with it without messing with the smart pointer. Both std::unique_ptr and std::shared_ptr have the get() member function that returns the stored pointer. For example:

```cpp
std::unique_ptr<Object> pu = std::make_unique<Object>();
function(*pu.get());  // Usage of (6)
function(pu.get());   // Usage of (7)
```
A `std::weak_ptr` must be converted to a `std::shared_ptr` first in order to take the stored pointer.

## Return smart pointers from functions
return smart pointers if the caller wants to manipulate the smart pointer itself, return raw pointers/references if the caller just needs a handle to 
the underlying object.
If you really need to return smart pointers from a function, always return by value.

```cpp
std::unique_ptr<Object> getUnique();
std::shared_ptr<Object> getShared();
std::weak_ptr<Object>   getWeak();
```

There are at least three good reasons for this:


1) Once again, smart pointers are powered by move semantics: the dynamically-allocated resource they hold is moved around, not wastefully copied.

2) modern compilers play the Return Value Optimization (RVO) trick. They are able to detect that you are returning an object by value, and they apply a sort of return shortcut to avoid useless copies. Starting from C++17, this is guaranteed by the standard. So even the smart pointer itself will be optimized out.

3) returning `std::shared_ptr` by reference doesn't properly increment the reference count, which opens up the risk of deleting something at the wrong time.

Thanks to point 2. you don't need move anything when returning a std::unique_ptr:
```cpp
std::unique_ptr<Object> getUnique()
{
    std::unique_ptr<Object> p = std::make_unique<Object>();
    return p; 
    // also return std::make_unique<Object>();
}
```


## Reference

Basically pointers can be reassigned to different location in memory but references can only stick to one variable. 
reference is like an alias for an existing variable, it shares the same address as the original variable.
also a reference can not be null.

```cpp
int foo=2;
int &ref=foo;
int *ptr=&foo;
foo++;
```
so the values are:

```
ref is: 3
foo is: 3
&ref is: 0x7ffee0953bdc
&foo is: 0x7ffee0953bdc
ptr is: 0x7ffee0953bdc
*ptr is: 3
```
`&foo` and  `&ref` are the same, because reference is just like an alias for variable. Pointer is a variable that store the address of an other variable, so it can be changed but reference can not be changed. The followings  will rise an error (we need rvalue ref):

```cpp
int & r3 = 200;
```

uninitialized reference:  

```cpp
& ref = x;
```
uninitialized reference:  

```cpp
int &ref_2;                 
```

Refs: 
        [1](https://www.internalpointers.com/post/move-smart-pointers-and-out-functions-modern-c),
        [2](https://www.acodersjourney.com/top-10-dumb-mistakes-avoid-c-11-smart-pointers/),
        [3](https://www.learncpp.com/cpp-tutorial/103-aggregation/),
        [4](https://www.nextptr.com/tutorial/ta1441164581/stdref-and-stdreference_wrapper-common-use-cases)
        
        
[source code](../src/pointers/pointers.cpp)        



## lvalues references and rvalues references

lvalue is an object that occupies some identifiable address in memory (heap, stack) and not in CPU register. lvalues can be written on the left of assignment operator `=`. rvalue is, the simple explanation is that it doesn't have a memory address. i.e. the number 4. 
Reference in C++ is an alias for an existing variable, to the already existing variable. They are declared using the `&` before the name of the variable.

lvalue reference:
```cpp
int x= 1;
int& y = x;
```

Refs: [1](https://www.geeksforgeeks.org/lvalues-references-and-rvalues-references-in-c-with-examples)


## reference wrapper
std::vector and std::list elements cannot be references, because:
1. references have to be initialized.
2. they cannot be reassigned, so the following won't compile:

```cpp
std::vector<person&> m_people{};
```

or 
```cpp
std::vector<<std::unique_ptr<person>>> people;
```
or 
```cpp
std::vector<<const person> people;
```


Essentially, std::reference_wrapper is a class that acts like a reference, but also allows assignment and copying, so it’s compatible with std::lists and std::vector.

```cpp
std::vector<std::reference_wrapper<std::unique_ptr<person>>> people;
std::unique_ptr empolyee1= std::make_unique<person>();
people.push_back(empolyee1 );
```

## removing reference/ pointer 

```cpp
std::cout<< std::is_same<int, std::remove_pointer<int>::type>();
std::cout<< std::is_same<int, std::remove_reference<int &>::type>();
```


## Atomic Smart Pointers

Refs: [1](https://www.modernescpp.com/index.php/atomic-smart-pointers)

