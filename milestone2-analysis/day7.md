# Day 7: Testing Type Analysis

In this lab, you develop a test suite for type analysis.
The test suite consists of type projection test cases, resolution test cases and constraint test cases.

## Overview

### Objectives

Develop a test suite for type analysis.
The test suite should provide

1. Test cases for the types of expressions.
1. Test cases for the resolution of method names.
2. Test cases for
  * errors on duplicate method definitions,
  * errors on missing method definitions,
  * errors on overloaded methods,
  * notes on overriding methods,
  * type errors in expressions,
  * type errors in statements, and
  * type errors in method definitions.

### Submission

You need to submit your test project on Blackboard.
As part of your submission, we ask you to provide a short paragraph explaining the organisation of your test project.
The deadline for submission is November 20, 17:59.

### Grading

You can earn up to 5 points for the organisation of your test project.
We take its structure, file names, test names, and your explanation into account.
You can earn up to 95 points for the coverage of your test cases.

## Detailed Instructions

### Testing Types of Expressions

In test cases for type analysis, 
 you write syntactically correct programs and
 mark expressions with inner square bracket blocks. 
You can then specify the expected type of the marked expression in a `run`-`to` clause.
For example, the following two test cases require an integer literal to be of type `IntType()` 
and a variable reference to be of its declared type `BoolType()`:

    module types
     
    language MiniJava
     
    start symbol Start
     
    test integer literal type [[
        class Main {
            public static void main (String[] args) {
                System.out.println([[1]]);
            }
        }
    ]] run get-type to Int()
     
    test variable reference type [[
        class Main {
            public static void main (String[] args) {
                System.out.println(new Foo().run());
            }
        }
     
    class Foo {
        public int run() {
            boolean x;
            int y;
             
            if ([[x]]) 
                y = 1;
            else
                y = 0;
             
            return y;
        }
    }
    ]] run get-type to Bool()

You can use `setup` headers and footers to avoid repeating parts in similar test cases.

You should come up with test cases for the types of all kinds of expressions.
Just like previous testing assignments, this assignment is all about the coverage of your test suite.

### Testing Method Name Resolution

We covered name resolution tests already in a previous assignment.
We skipped method names in that assignment, since method name resolution requires type analysis.
Consider the following test case as an example:

    test method name resolution [[
        class Main {
            public static void main (String[] args) {
                System.out.println(new Foo().[[run]]());
            }
        }
     
    class Foo {
        public int [[run]]() {
            return 1;
        }
    }
    ]] resolve #1 to #2

The type of the callee expression determines the class in which the method declaration can be found.
In this example, the expression `new Foo()` is of type `ClassType(Foo)` and 
the corresponding class `Foo` contains a method declaration for `run()`.

You should come up with test cases for the resolution of method names. 
Start with simple test cases, but keep in mind that method name resolution is quite complex 
 and that coverage is the main criterion for your grade. 
It is important to think about forward and backward references, 
 resolution in the presence of homonyms and overriding, 
 and the influence of class hierarchies on resolution.

You should also come up with test cases for error checking on method names.
This should include test cases for errors on duplicate definitions, missing definitions, and method overloading 
 as well as for notes on method overriding.
Similar to previous test cases, you can pair up positive (`0 errors`) and negative test cases.

### Testing Type Error Checking

A type error occurs, when the type of an expression does not conform to its expected type.
Consider the following test case as an example:

    test print boolean [[
        class Main {
            public static void main (String[] args) {
                System.out.println([[true]]);
            }
        }
    ]] 1 error

In MiniJava, `System.out.println()` can only print integers. 
Thus, there should be an error on `true`, when we pass it to the print statement.
Similarly, type errors can occur in other statements, expressions, and method declarations.
You should come up with test cases for such errors.
Again, keep in mind that coverage is the main criterion for your grade.


