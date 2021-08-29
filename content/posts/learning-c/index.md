---
title: "Introduction to C Programming"
date: 2021-08-25T15:02:07+01:00
draft: true
tags: ["C Programming"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "My introduction to the C programming language"
    relative: false
---

## Introduction

I've been coding in PowerShell for many years and wanted to step back and learn the basics of a language which many future languages were based; C++, C#, PowerShell, Go, etc. The biggest hurdle to get over was the change from an object-orientated language, such as PowerShell, to a functional language in C.

[Wikipedia's](https://en.wikipedia.org/wiki/C_(programming_language)) description of C:

_"C is a general-purpose, procedural computer programming language supporting structured programming, lexical variable scope, and recursion, with a static type system."_

I won't go into too much detail as C is not a language I will ever code in. But learning it has been the perfect introduction to learning Go which is syntactically similar to C, and a modern language gaining much traction with developers and DevOps engineers. I'd like to create similar post in the future for Go and Python.

So here are some of things I have learned about the C programming language.

## Compiling C Code

All C code needs to be compiled from source in order to be executed. On Windows you can use Visual Studio, or on Linux you can use `gcc` using the command below. 

```bash
gcc -o test ./test.c
```

The output flag (`-o`) of `test` is the name of the compiled file to be executed. The `test.c` file is the name of the C source code file from which to build from.

To run the compiled program from a Linux terminal simply type

```bash
./test
```

## Hello World

Here is a basic hello world program.

``` C
#include <stdio.h>

int main()
{
    printf("hello, world\n");
    printf("hello again!!\n");
    return 0;
}
```

It starts by defining the `stdio.h` header file in the `#include` pre-processor, because the `printf()` function is found in the `stdio.h` header file.

Next is the `main()` function. The execution of all C programs begin here. Functions can be created outside of `main()`, but are executed within it. The main function starts with `int` as it is expected to return an integer.

Then on to the `printf()` function. The argument in the brackets is a string wrapped in double quotes (""). But notice before the end of the string is the `\n` escape sequence. This tells the program to add a new line in the terminal. The semi-colon indicates the end of the command.

And finally within the `main()` function is the `return` command. The value of zero is the `int` type the function was expecting to be returned. Zero means the program completed successfully.

After compiling and running this program, the following will be returned.

```termial
./helloworld

hello, world
hello again!!

```

## Conversion Characters

This was a strange one to get my head around initially. Conversion characters are like placeholders for variables or data you want to display. They are percent symbols followed by a letter. The data that you want in their place is at the end of the line. Some examples of conversion characters are:

| Conversion Character | Variable or data type |
| -------------------- | --------------------- |
| %s                   | String                |
| %i                   | integer               |
| %f                   | float                 |
| %c                   | Single Character      |

There are other which I am unlikely to ever use. The `%%` character is used to mean percent (%).

The below is an example of using conversion characters in the `printf()` function.

``` C
// prints a character and numbers
#include <stdio.h>

int main(void)
{
    printf("A test char of %c\n", 'B');
    printf("A test int of %d\n", 87);
    printf("A test float of %.1f\n", 85.9);
    return 0;
}
```

At the start of the program is two `//` forward slashes, followed by text. These are comments which the program ignores.

Notice a `char` requires single quotes `'B'`, and an `int` or `float` does not. And a string requires double quotes "".

The one in the float conversion character `%.1f`, means show one character after the decimal point.

After compiling and running this program, the following will be returned.

```termial
./con_char

A test char of B
A test int of 87
A test float of 85.9

```

## For loop

A loop allows you to repeat a block of code until a condition is met. In the example below I want the program to print "This is a Test" 10 times, each on a new line.

``` C
#include <stdio.h>
// Prints "This is a Test!" 10 times

int main(void)
{
    for (int i = 0; i < 10; i++)
    {
        printf("This is a Test!\n");
    }
}
```

From the `for` line, first the integer type variable of `i` is set as zero. This is the initialisation statement which will only run once. Then the test expression of `i < 10` is evaluated which will return as `true` because `i` is currently less than ten as we know it is zero. And finally the update statement of `i++` is run which increments `i` by one. As the test expression returned true the code in the for loop can run.

On the second run we know the initialisation statement will not run again. So this time as `i` was incremented by one we know the value is now two. So as two is less than ten it returns to true, `i++` increments the variable from two to three and the `printf()` function runs again.

This will happen ten times in total until on the eleventh run, `i` will be equal to ten, which we know is not less than ten, so will return `false` on the test expression. The loop completes.

> Note: Be careful not to create an infinite loop where the test expression will always return true. This can happen if you do not correctly define the initialisation statement, test expression or update statement.

After compiling and running this program, the following will be returned.

```termial
./testloop

This is a Test!
This is a Test!
This is a Test!
This is a Test!
This is a Test!
This is a Test!
This is a Test!
This is a Test!
This is a Test!
This is a Test!

```

## Variables and Constants

``` C
#include <stdio.h>
#include <string.h>
#define NAME "Mark Kerry"

// NAME constant defined above

int n = 0;      /* Global Variables - before the main() function */
char x = 'X';

int main(void)
{
    long int l = 500000;    /* Local Variables - in the main() function */
    float f = 0.0002;

    /* There is not a default string variable. You have to create a char array.
    This can be done in a few different ways
    */
    char name[11] = "Mark Kerry"; 
    char name2[] = "Mark Kerry";
    char name3[11];
    strcpy(name3, "Mark Kerry");
    
    /* Code goes here */
    
    return 0;
}
```

## The if, else Statements

The if statement uses relational operators to check whether something is true and if so, executes code. If the statement is false the else statement to executes different code. 

``` C
int a;
int b;

printf("Please enter a positive number: ");
scanf("%d", &a);
printf("Please enter another positive number: ");
scanf("%d", &b);
    
if (a < b)
{
    printf("%d is less than %d\n", a, b);
}
else if (a == b)
{
    printf("%d is equal to %d\n", a, b);
}
else
{
    printf("%d is more than %d\n", a, b);
}
```

## Logical Operators

```C
if ((num >= 10) && (num <= 100))
{
    // num is between 10 and 100
}

if ((num == 0) || (num2 > 1000))
{
    // do something
}
```

## Key

| Examples                | Description               |
|-------------------------|---------------------------|
| #include #define        | Pre-processor Directives  |
| stdio.h math.h string.h | Header Files              |
| main() printf()         | Functions                 |
| return while float      | Commands                  |
| if elseif else          | Statements                |
| string int float        | Data Types                |
| %d %f %c %s             | Conversion Characters     |
| \n \a \t                | Escape Sequences          |
| && !                    | Logical Operators         |
| == > < >= !=            | Relational Operators      |
| &  *                    | Pointer Operators         |
| *= /= %= += -=          | Compound Operators        |
| /* This is */           | Comments                  |
| (5 * 2 - 6)             | Expression                |
