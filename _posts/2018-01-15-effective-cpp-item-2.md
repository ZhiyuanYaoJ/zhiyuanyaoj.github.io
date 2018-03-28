---
title: Effective C++ - Item 2 - Prefer consts, enums, and inlines to #defines
date: 2018-01-15 12:31:00
description: Notes taken when reading Effective C++ (3rd Edition), Chapter 1 - Item 2 - Prefer consts, enums, and inlines to #defines
categories:
- C++
tags:
- C++
photos:
---

## Intro - Problem of _#defines_

This Item might better be called “_prefer the compiler to the preprocessor_,”
because `#define` may be treated as if it’s not part of the language
_per se_. That’s one of its problems. 

```c++ 
//  E.g.:
    #define ASPECT_RATIO 1.653
```

> Notes: 
> 1. The symbolic name `ASPECT_RATIO` may never be seen by compilers (It may be removed by the preprocessor before the source code ever gets to a compiler)
> 2. As a result, the name `ASPECT_RATIO` may not get entered into the symbol table

This can be confusing if you get an error during compilation involving the use of the constant, because the error message may refer to `1.653`, not `ASPECT_RATIO`. If `ASPECT_RATIO` were defined in a header file you didn’t write, you’d have no idea where that `1.653` came from, and you’d waste time tracking it down. This problem can also crop up in a symbolic debugger, because, again, the name you’re programming with may not be in the symbol table.

## Solution

### _const_

The first solution is to replace the macro with a constant:

```c++
    const double AspectRatio = 1.653;   // uppercase names are usually for
                                        // macros, hence the name change
```

> Notes:
> 1. As a language constant, `AspectRatio` is definitely seen by compilers and is certainly entered into their symbol tables.
> 2. In the case of a floating point constant (such as in this example), use of the constant may yield smaller code than using a #define. 
>   > That’s because the preprocessor’s blind substitution of the macro name `ASPECT_RATIO` with `1.653` could result in multiple copies of `1.653` in your object code, while the use of the constant `AspectRatio` should never result in more than one copy.

When replacing `#defines` with constants, two special cases are worth mentioning: 

1. Defining constant pointers
2. Class-specific constants

#### Defining constant pointers

It’s important that the _pointer_ be declared **const**.

> Notes:
> 1. Because constant definitions are typically put in header files (where many different source files will include them)
> 
> 2. Usually in addition to what the pointer points to. 
>   > E.g. To define a constant `char*`-based string in a header file, you have to write const twice:
>   > 
>   > ```c++
>   >     const char * const authorName = "Scott Meyers";
>   > ```
>   > 
>   > However, it’s worth reminding you here that string objects are generally preferable to their `char*`-based progenitors, so `authorName` is often better defined this way:
>   > 
>   > ```c++
>   >     const std::string authorName("Scott Meyers");
>   > ```
> 
> 3. For a complete discussion of the meanings and uses of `const`, especially in conjunction with pointers, see [Item 3](../Item_3/README.md). 

#### Class-specific constants

1. To limit the scope of a constant to a class: must make it a member
2. To ensure there's at most one copy of the constant: must make it a _static_ member

```c++
    class GamePlayer {
    private:
        static const int NumTurns = 5;  // constant declaration
        int scores[NumTurns];           // use of constant
        ...
    };
```

> Notes:
> 1. Above is a _declaration_ for `NumTurns`, not a _definition_. (What is definition? See [Item 1](../Item_1/README.md))
> 
> 2. Usually, C++ requires that you provide a definition for anything you use, but class-specific constants that are static and of integral type (e.g., integers, `chars`, `bools`) are an exception. 
>   > - As long as you don’t take their address, you can declare them and use them without providing a definition.
>   > - If you do take the address of a class constant, or if your compiler incorrectly insists on a definition even if you don’t take the address, you provide a separate definition like this:
>   > 
>   > ```c++
>   >     const int GamePlayer::NumTurns;     // definition of NumTurns; see
>   >                                         // below for why no value is given
>   > ```
>   > 
>   >   > You put this in an implementation file, not a header file. Because the initial value of class constants is provided where the constant is declared (e.g., `NumTurns` is initialized to `5` when it is declared), no initial value is permitted at the point of definition. 
> 
> 3. `#defines` can't be used for
>   > - Class-specific constant:
>   > - Any kind of encapsulation, i.e., there is no such thing as a “private” #define.
>   > 
>   > > Because `#defines` don’t respect scope. Once a macro is defined, it’s in force for the rest of the compilation (unless it’s `#undefed` somewhere along the line). 
>   > > 
>   > > However, of course, const data members can be encapsulated; `NumTurns` is.
> 
> 4. Older compilers may not accept the syntax above, because it used to be illegal to provide an initial value for a static class member at its point of declaration. Furthermore, in-class initialization is allowed only for integral types and only for constants. In cases where the above syntax can’t be used, you put the initial value at the point of definition:
> 
> ```c++
>   class CostEstimate {
>   private:
>   static const double FudgeFactor;    // declaration of static class
>   ...                                 // constant; goes in header file
>   };
>   
>   const double                        // definition of static class
>   CostEstimate::FudgeFactor = 1.35;   // constant; goes in impl. file
> ```

### _enum_

Above is all you need almost all the time. The only exception is when you need the value of a class constant during compilation of the class, such as in the declaration of the array `GamePlayer::scores` above (where compilers insist on knowing the size of the array during compilation). Then the accepted way to compensate for compilers that (incorrectly) forbid the in-class specification of initial values for static integral class constants is to use what is affectionately (and non-pejoratively) known as _“the enum hack.”_ This technique takes advantage of the fact that **the values of an enumerated type can be used where ints are expected**, so `GamePlayer` could just as well be defined like this:

```c++
    class GamePlayer {
    private:
    enum { NumTurns = 5 };      // “the enum hack” — makes
                                // NumTurns a symbolic name for 5
    int scores[NumTurns];       // fine
    ...
    };
```

The enum hack is worth knowing about for several reasons:
1. The enum hack behaves in some ways more like a #define than a const does, and sometimes that’s what you want. 
> For example, it’s legal to take the address of a const, but it’s not legal to take the address of an `enum`, and it’s typically not legal to take the address of a `#define`, either. If you don’t want to let people get a _pointer_ or reference to one of your integral constants, an `enum` is a good way to enforce that constraint.(For more on enforcing design constraints through coding decisions, consult [Item 18](../Item_18/README.md).) 
> 
> Also, though good compilers won’t set aside storage for const objects of integral types (unless you create a pointer or reference to the object), sloppy compilers may, and you may not be willing to set aside memory for such objects. Like `#defines`, `enums` never result in that kind of unnecessary memory allocation.

2. A second reason to know about the enum hack is purely pragmatic. Lots of code employs it, so you need to recognize it when you see it. In fact, the enum hack is a fundamental technique of template metaprogramming (see [Item 48](../Item_48/README.md)).

## Functions

Getting back to the preprocessor, another common (mis)use of the `#define` directive is using it to implement macros that look like functions but that don’t incur the overhead of a function call. Here’s a macro that calls some function `f` with the greater of the macro’s arguments: 

```c++
    // call f with the maximum of a and b
    #define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))
```

Macros like this have so many drawbacks:
1. Whenever you write this kind of macro, you have to remember to parenthesize all the arguments in the macro body. 
2. Otherwise you can run into trouble when somebody calls the macro with an expression.
3. But even if you get that right, look at the weird things that can happen:

> ```c++
>     int a = 5, b = 0;
>     CALL_WITH_MAX(++a, b); // a is incremented twice
>     CALL_WITH_MAX(++a, b+10); // a is incremented once
> ```
> 
> Here, the number of times that a is incremented before calling `f` depends on what it is being compared with! 

Fortunately, you don’t need to put up with this nonsense. You can get all the efficiency of a macro plus all the predictable behavior and type safety of a regular function by using a template for an `inline` function (see [Item 30](../Item_30/README.md)): 

```c++
    template<typename T>                            // because we don’t
    inline void callWithMax(const T& a, const T& b) // know what T is, we
    {                                               // pass by reference-tof(
        a > b ? a : b);                             // const — see Item20
    }
```

> Notes:
> 1. This template generates a whole family of functions, each of which takes two objects of the same type and calls `f` with the greater of the two objects. 
> 2. There’s no need to parenthesize parameters inside the function body, no need to worry about evaluating parameters multiple times, etc. 
> 3. Furthermore, because `callWithMax` is a real function, it obeys scope and access rules. 
>   > For example, it makes perfect sense to talk about an inline function that is private to a class. In general, there’s just no way to do that with a macro. 

## Summary

Given the availability of `consts`, `enums`, and `inlines`, your need for the preprocessor (especially #define) is reduced, but it’s not eliminated. `#include` remains essential, and `#ifdef/#ifndef` continue to play important roles in controlling compilation. It’s not yet time to retire the preprocessor, but you should definitely give it long and frequent vacations.

**Things to Remember**

- For simple constants, prefer const objects or enums to #defines.
- For function-like macros, prefer inline functions to #defines.

## Navigate
* [Github - Reading-Notes - Effective C++ - Item 2](https://github.com/ZhiyuanYaoJ/Reading-Notes/tree/master/Computer%20Science/Effective%20C%2B%2B/Item_2/README.md)