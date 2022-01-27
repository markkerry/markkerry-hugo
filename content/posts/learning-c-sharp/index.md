---
title: "Introduction to C#"
date: 2021-11-01T12:27:31Z
draft: true
tags: ["C#"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "My introduction to the C# programming language"
    relative: false
---

In my previous Introduction to C Programming post I covered some coding fundamentals which I won't repeat in this one. Instead, I will go over some differences between C and C#, such as OOP, and then using the Azure .NET SDK.

[Wikipedia's](https://en.wikipedia.org/wiki/C_Sharp_(programming_language)) description of C#:

_"C# is a general-purpose, multi-paradigm programming language. C# encompasses static typing, strong typing, lexically scoped, imperative, declarative, functional, generic, object-oriented (class-based), and component-oriented programming disciplines."_

## Object Orientated Programming

## Classes

## Static Keyword

## Public/Private

## Methods

In Object-Orientated Programming, the term method is used instead of Function, and they are associated with a Class. Unlike in C where `main` is a function. In C# a method is within a Class.

```csharp
class Program
{
    static void Main(string[] args)
}
```

## Return Types

The `return` command can return information back to the caller.

`void` means nothing to be returned

`static int` means return an integer

## Properties

## Get and Set

## Handling Exceptions

## Notes

`using System` statement means to use code from the .Net System namespace

`namespace` helps organise programs to prevent names inside your code from conflicting with names from other libraries.

`class`. All C# is OOP and all code is organised into classes.

`main` function/method is the entry point into every C# program.

`Console.WriteLine("");`. The `Console` object originated from the `System` namespace and the `.WriteLine()` is a method in the console object. The semi-colon completes the statement.

