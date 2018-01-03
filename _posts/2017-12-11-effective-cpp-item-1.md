---
title: Effective C++ - Item 1 - View C++ as a federation of languages
date: 2017-12-09 14:00:00
description: Notes taken when reading Effective C++ (3rd Edition), Chapter 1 - Item 1 - View C++ as a federation of languages.
categories:
- C++
tags:
- C++
photos:
---

## Intro

On basis of C, C++ have features tacked on:

- **Object-oriented features**: C++'s original name was "C with Classes"
- **Exceptions**: which required different approaches to structuring functions
- **Templates**: which gave rise to new ways of thinking about design
- **STL**: which defined an approach to extensibility

> Notes:
> 1. For *exceptions*, see [Item 29](../Item_29/README.md)
> 2. For *templates*, see [Item 41](../Item_41/README.md) 

C++ is a multiparadigm programming language, which supports:

- prodedual features
- object-oriented features
- functional features
- generic features
- and metaprogramming features.

Hence, the easiest way is to view C++ not as a single language but as a federation of related languages

## Federation

To make sense of C++, you have to recognize its primary sub- languages. Fortunately, there are only four:
- **C**
- **Object-Oriented C++**
- **Template C++**
- **The STL**

### C

Way down deep, C++ is still based on C:

- blocks
- statements 
- the preprocessor 
- built-in data types 
- arrays
- pointers 
- etc. 

all come from C. In many cases, C++ offers approaches to problems that are superior to their C counterparts (e.g., see [Items 2](../Item_2/README.md) (alternatives to the preprocessor) and [Item 13](../Item_13/README.md) (using objects to manage resources)), but when you find yourself working with the C part of C++, the rules for effective programming reflect C’s more limited scope: 

- no templates
- no exceptions
- no overloading 
- etc.

### Object-Oriented C++

This part of C++ is what C with *Classes* was all about: 

- classes (including constructors and destructors) 
- encapsulation
- inheritance
- polymorphism
- virtual functions (dynamic binding) 
- etc. 

This is the part of C++ to which the classic rules for **object-oriented design** most directly apply.

### Template C++

This is the **generic programming** part of C++, the one that most programmers have the least experience with. Template considerations pervade C++, and it’s not uncommon for rules of good programming to include special template-only clauses (e.g., see [Item 46](../Item_46/README.md) on facilitating type conversions in calls to template functions). In fact, templates are so powerful, they give rise to a completely new programming paradigm, *template metaprogramming* (TMP). [Item 48](../Item_48/README.md) provides an overview of TMP, but unless you’re a hard-core template junkie, you need not worry about it. The rules for TMP rarely interact with mainstream C++ programming.

### The STL

The STL is a template library, of course, but it’s a very special template library. Its conventions regarding 

- containers
- iterators
- algorithms
- and function objects 

mesh beautifully, but templates and libraries can be built around other ideas, too. The STL has particular ways of doing things, and when you’re working with the STL, you need to be sure to follow its conventions.

## Summary

Keep these four sublanguages in mind, and don’t be surprised when you encounter situations where effective programming requires that you change strategy when you switch from one sublanguage to another. 

> For example, *pass-by-value* is generally more efficient than *pass-by-reference* for **built-in** (i.e., **C-like**) types. 

> But when you move from the C part of C++ to **Object-Oriented C++**, the existence of *user-defined constructors* and *destructors* means that *pass-by-reference-to-const* is usually better. 

> This is especially the case when working in **Template C++**, because there, you don’t even know the type of object you’re dealing with. 

> When you cross into the **STL**, however, you know that *iterators* and *function objects* are modeled on *pointers* in C, so for iterators and function objects in the STL, the old C *pass-by-value* rule applies again. 

> *Note: For all the details on choosing among parameter-passing options, see [Item 20](../Item_20/README.md).

C++, then, isn’t a unified language with a single set of rules; it’s a federation of four sublanguages, each with its own conventions. Keep these sublanguages in mind, and you’ll find that C++ is a lot easier to understand.

> **Remember**:
> Rules for effective C++ programming vary, depending on the part of C++ you are using.

## Navigate
* [Github - Reading-Notes - Effective C++ - Item 1](https://github.com/EricYaoJ/Reading-Notes/tree/master/Computer%20Science/Effective%20C%2B%2B/Item_1/README.md)