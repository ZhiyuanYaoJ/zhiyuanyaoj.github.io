---
title: Effective C++ - Intro
date: 2017-12-09 14:00:00
description: Notes taken when reading Effective C++ (3rd Edition), Chapter 0 - Intro. Covering terminology, naming conventions, threading considerations, and tr1 & boost libraries.
categories:
- C++
tags:
- C++
photos:
---

## Terminology

### Declaration

A **declaration** tells compilers about the name and type of something,
but it omits certain details. These are declarations:

```c++
    extern int x;                           // object declaration
    
    std::size_t numDigits(int number);      // function declaration
    
    class Widget;                           // class declaration
    
    template<typename T>                    // template declaration
    class GraphNode;                        // (see Item 42 for info on
                                            // the use of “typename”)
```

> Note:
> 1. `int` is referred as an "object", even though it's of built-in type. The name "object" here is not reserved for variables of user-defined type.
> 2. `std::size_t` is the type `size_t` in namespace `std`. 
> 3. `std` is the namespace where virtually everything in C++'s standard library (the one from C89, to be precise) is located. 
> 4. `size_t` is just a typedef for some unsigned type that C++ uses when counting things (e.g., the number of characters in a char*- based string, the number of elements in an STL container, etc.). It’s also the type taken by the operator[] functions in vector, deque, and string, a convention we’ll follow when defining our own operator[] functions in [Item 3](../Item_3/README.md).
> 5. `template<typename T> class GraphNode;` see [Item 42](../Item_42/README.md) for info on the use of "typename".


### Signature

Each function’s declaration reveals its **signature**, i.e. 

- its parameter
- its return types

> Note:
> 1. A function’s signature is the same as its type. 
> 2. In the case of `numDigits` above, the signature is `std::size_t (int)`, i.e., “function taking an int and returning a std::size_t.” 
> 3. The official C++ definition of “signature” excludes the function’s return type, but it’s more useful to have the return type be considered part of the signature.


### Definition

A **definition** provides compilers with the details a declaration omits. 

```c++
    int x;                                  // object definition
    
    std::size_t numDigits(int number) {     // function definition.
        std::size_t digitsSoFar = 1;        // (This function returns
        while ((number /= 10) != 0)         // the number of digits
            ++digitsSoFar;                  // in its parameter.)
        return digitsSoFar;
    }
    
    class Widget {                          // class definition
    public:
        Widget();
        ~Widget();
        ...
    };

    template<typename T>                    // template definition
    class GraphNode {
    public:
        GraphNode();
        ~GraphNode();
        ...
    };
```

> Note:
> 1. For an object, the definition is where compilers assign memory for the object. 
> 2. For a function or a function template, the definition provides the code body. 
> 3. For a class or a class template, the definition lists the members of the class or template.


### Initialization

**Initialization** is the process of giving an object its first value. For objects generated from structs and classes, initialization is performed by
constructors. 

#### Default Constructor

A **default constructor** is one that can be called without any arguments. Such a constructor either has no parameters or has a default value for every parameter.

```c++
    class A {
    public:
        A();                                    // default constructor
    };

    class B {
    public:
        explicit B(int x = 0, bool b = true);   // default constructor
    };

    class C {
    public:
        explicit C(int x);                      // not a default constructor
    };
```

> Note:
> The constructors for classes B and C are declared explicit here. That prevents them from being used to perform implicit type conversions, though they may still be used for explicit type conversions. (See below)

```c++
    void doSomething(B bObject);            // a function taking an object of
                                            // type B
    
    B bObj1;                                // an object of type B
    
    doSomething(bObj1);                     // fine, passes a B to doSomething
    
    B bObj2(28);                            // fine, creates a B from the int 28
                                            // (the bool defaults to true)
    
    doSomething(28);                        // ERROR! doSomething takes a B,
                                            // not an int, and there is no
                                            // implicit conversion from int to B
    
    doSomething(B(28));                     // fine, uses the B constructor to
                                            // explicitly convert (i.e., cast) the
                                            // int to a B for this call. (See
                                            // Item27 for info on casting.)
```

> Note:
> 1. Constructors declared explicit are usually preferable to non-explicit ones, because they prevent compilers from performing unexpected (often unintended) type conversions.
> 2. Unless there's a good reason for allowing a constructor to be used for implicit type conversions, always declare it explicit.
> 3. `doSomething(B(28))` uses "cast" to explicitly convert, see [Item 27](../Item_27/README.md) for info on casting


#### Copy Constructor & Copy Assignment Operator

The **copy constructor** is used to initialize an object with a different object of the same type, and the **copy assignment operator** is used to copy the value from one object to another of the same type:

```c++
    class Widget {
    public:
        Widget();                               // default constructor
        Widget(const Widget& rhs);              // copy constructor
        Widget& operator=(const Widget& rhs);   // copy assignment operator
        ...
    };

    Widget w1;                                  // invoke default constructor
    Widget w2(w1);                              // invoke copy constructor
    w1 = w2;                                    // invoke copy assignment operator
```

> Note:
> 1. Read carefully when you see what appears to be an assignment, because the “=” syntax can also be used to call the copy constructor:
> `Widget w3 = w2;                              // invoke copy constructor!`
> 2. Fortunately, copy construction is easy to distinguish from copy assignment. If a new object is being defined (such as w3 in the statement above), a constructor has to be called; it can’t be an assignment. 
> 3. If no new object is being defined (such as in the “w1 = w2” statement above), no constructor can be involved, so it’s an assignment.

The copy constructor is a particularly important function, because it defines how an object is passed by value. For example, consider this:

```c++
    bool hasAcceptableQuality(Widget w);
    ...
    Widget aWidget;
    if (hasAcceptableQuality(aWidget)) ...
```

> Note:
> 1. The parameter `w` is passed to `hasAcceptableQuality` by value, so in the call above, `aWidget` is copied into `w`. 
> 2. The copying is done by `Widget`’s copy constructor. Pass-by-value means call the copy constructor.”
> 3. However, it’s generally a bad idea to pass user-defined types by value.
*Pass-by-reference-to-const* is typically a better choice. For details, see
[Item 20](../Item_20/README.md).


### STL

The **STL** is the Standard Template Library, the part of C++’s standard
library devoted to:

- containers *(e.g., `vector`, `list`, `set`, `map`, etc.)*;
- iterators (e.g., `vector<int>::iterator`, `set<string>::iterator`, etc.);
- algorithms (e.g., `for_each`, `find`, `sort`, etc.);
- and related functionality. 

> Note:
> 1. Much of that related functionality has to do with **function objects**: objects that act like functions. Such objects come from classes that overload operator(), the function call operator. 
> 2. STL is too ***useful*** not to take advantage of it!


### Undefined Behavior

For a variety of reasons, the behavior of some constructs in C++ is literally not defined: one can’t reliably predict what will happen at runtime. Here are two examples of code with **undefined behavior**:

```c++
    int *p = 0;                             // p is a null pointer
    std::cout << *p;                        // dereferencing a null pointer
                                            // yields undefined behavior

    char name[] = "Darla";                  // name is an array of size 6 (don’t
                                            // forget the trailing null!)
    char c = name[10];                      // referring to an invalid array index
                                            // yields undefined behavior
```

> Note:
> 1. The results of undefined behavior are not predictable and may be very **unpleasant**, e.g. programs with undefined behavior can erase our hard drive (though not probable).
> 2. Programs with undefined behavior may behave erratically, sometimes running normally, other times crashing, still other times producing incorrect results.
> 3. We should try our best to steer clear of undefined behavior.


### Interface

Java and the .NET languages offer **Interfaces** as a language element, but there is no such thing in C++, though [Item 31](../Item_31/README.md) discusses how to approximate them. When refering to the term "interface", we are generally talking about:

- a function's signature;
- the accessible elements of a class (e.g., a class’s “public interface,” “protected interface,” or “private interface”)
- the expressions that must be valid for a template’s type parameter (see
[Item 41](../Item_41/README.md))

> Note:
> We are talking about interfaces as a fairly general **design idea**!


### Client

A **client** is someone or something that uses the code (typically the interfaces) we write. For example:

- The clients of a function: 
    + the parts of the code that call the function (or take its address)
    + the humans who write and maintain such code. 
- The clients of a class or a template:
    + the parts of the software that use the class or template
    + the programmers who write and maintain that code. 

> Note:
> 1. When discussing clients, we typically focus on programmers, because programmers can be confused, misled, or annoyed by bad interfaces, while, the code they write can’t be. 
> 2. Bear in mind, you are a client of the software other people develop. Wouldn’t you want those people to make things easy for you? Besides, at some point you’ll almost certainly find yourself in the position of being your own client (i.e., using code you wrote), and at that point, you’ll be glad you kept client concerns in mind when developing your interfaces.


### Others

Distinction between:
- *functions* & *function templates*
- *classes* & *class templates*
is often glossed over.

> Note:
> 1. Because what's **true about one** is **often true about the other**.
> 2. In situations where this is not the case, I distinguish among classes, functions, and the templates that give rise to classes and functions. 

Some abbreviations:
- constructors: ***ctor***
- destructors: ***dtor***


## Naming Conventions

### lhs & rhs

- `lhs`: "left-hand side"
- `rhs`: "right-hand side"

They are often used as parameter names for functions implementing binary operators, e.g., `operator==` and `operator*`. 

For example, if `a` and `b` are objects representing rational numbers, and if `Rational` objects can be multiplied via a non-member `operator*` function (as [Item 24](../Item_24/README.md) explains is likely to be the case), the expression:
```c++
    a * b
```
is equivalent to the function call:
```c++
    operator*(a, b)
```

In [Item 24](../Item_24/README.md), `operator*` is declared like this:
```c++
    const Rational operator*(const Rational& lhs, const Rational& rhs);
```

> Note:
> 1. The left-hand operand, `a`, is known as the `lhs` inside the function. And the right-hand operand, `b`, is known as the `rhs`.
> 2. For member functions, the left-hand argument is represented by the `this` pointer, so sometimes we use the parameter name `rhs` by itself. (See below)

```c++
    class Widget {
    public:
        Widget();                               // default constructor
        Widget(const Widget& rhs);              // copy constructor
        Widget& operator=(const Widget& rhs);   // copy assignment operator
        ...
    };
```


### Widget

"Widget" doesn't mean anything. It's just a name of an example class. It has nothing to do with widgets in GUI toolkits.


### Pointer

Pointers are often named following the rule that *a pointer to an object of type T is called `pt`, “pointer to T.”* For example:

```c++
    Widget *pw;                                 // pw = ptr to Widget
    
    class Airplane;
    Airplane *pa;                               // pa = ptr to Airplane

    class GameCharacter;
    GameCharacter *pgc;                         // pgc = ptr to GameCharacter
```

Similar convention for references: `rw` might be a reference to a `Widget` and `ra` a reference to an `Airplane`.


### Others

- `mf`: member functions.


## Threading Considerations

As a language, C++ has no notion of threads — no notion of concurrency of any kind, same for C++’s standard library. As far as C++ is concerned, multithreaded programs don’t exist.

And yet they do. Thread safety is an issue many programmers confront. We are going to point out places where the C++ constructs we examine are likely to cause problems in a threaded environment. While largely limiting ourselves to single-threaded considerations, we acknowledge the existence of multithreading and tries to point out places where thread-aware programmers need to take particular care in evaluating the advice we offer.


## TR1 & Boost

There are references to **TR1** and **Boost** throughout this note. Each
has an Item that describes it in some detail ([Item 54](../Item_54/README.md) for TR1, [Item 55](../Item_55/README.md) for Boost), which are unfortunately at the end of this note. 

> **TR1**:
> TR1 (“Technical Report 1”) is a specification for new functionality being added to C++’s standard library. This functionality takes the form of new class and function templates for things like hash tables, reference-counting smart pointers, regular expressions, and more. All TR1 components are in the namespace `tr1` that’s nested inside the namespace `std`.

> **Boost**
> Boost is an organization and a [web site](http://boost.org) offering portable, peer-reviewed, open source C++ libraries. Most TR1 functionality is based on work done at Boost, and until compiler vendors include TR1 in their C++ library distributions, the Boost web site is likely to remain the first stop for developers looking for TR1 implementations. Boost offers more than is available in TR1, however, so it’s worth knowing about in any case.


## Navigate
* [Github - Reading-Notes - Effective C++ - Intro](https://github.com/ZhiyuanYaoJ/Reading-Notes/blob/master/Computer%20Science/Effective%20C%2B%2B/Intro/README.md)