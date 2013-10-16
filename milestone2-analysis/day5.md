# Day 5: Testing Name Analysis

In this lab, you develop a test suite for name analysis.
The test suite consists of resolution test cases and constraint test cases.

## Overview

### Objectives

Develop a test suite for name analysis.
The test suite should provide

1. Test cases for the resolution of
  * class names,
  * field names,
  * parameter names, and
  * variable names.
2. Test cases for
  * errors on duplicate definitions,
  * errors on missing definitions,
  * warnings on unused definitions,
  * errors on cyclic inheritance,
  * errors on hiding fields,
  * warnings on hiding variables,
  * errors on instantiating or subclassing the main class.

### Submission

You need to submit your test project on Blackboard.
As part of your submission, we ask you to provide a short paragraph explaining the organisation of your test project.
The deadline for submission is October 23, 17:59.

### Grading

You can earn up to 5 points for the organisation of your test project.
We take its structure, file names, test names, and your explanation into account.
You can earn up to 95 points for the coverage of your test cases.

## Detailed Instructions

### Testing Reference Resolution

In test cases for reference resolution, 
 you write syntactically correct programs and
 mark names at definition and use sites with inner square bracket blocks. 
You can then relate the use site with the definition site in a `resolve ... to ...` clause, 
  using numbers to refer to the inner blocks. 
For example, the following two test cases require to resolve the type `Foo` to the name in the definition of class `Foo`:

    test forward class name resolution [[
      class Main {
        public static void main(String[] args) {
          System.out.println(1);
        }
      }
     
      class Foobar {
        [[Foo]] x;
      }
     
      class [[Foo]] {}
    ]] resolve #1 to #2
     
    test backward class name resolution [[
      class Main {
        public static void main(String[] args) {
          System.out.println(1);
        }
      }
     
      class [[Foo]] {}
     
      class Foobar {
        [[Foo]] x;
      }
    ]] resolve #2 to #1
 
 Tip: You can use setup headers and footers to avoid repeating parts in similar test cases.

### Testing Error Checking

In test cases for error checking, you need o specify the number of errors, warnings, or notes in a test case 
 in `... errors`, `... warnings`, or `... notes` clauses. 
You can use inner square bracket blocks to mark the positions where messages should be reported. 
For example, the following test cases specify a correct MiniJava program, 
 a program with two errors which are reported on the name of a duplicate class `Foo`, 
 and another program with a warning which is reported on the name of an unused class `Foobar`:
 
    test correct program [[
      class Main {
        public static void main(String[] args) {
          System.out.println(1);
        }
      }
     
      class Foo {}
     
      class Foobar {
        Foo x;
      }
    ]] 0 errors
     
    test error on duplicate class [[
      class Main {
        public static void main(String[] args) {
          System.out.println(1);
        }
      }
     
      class [[Foo]] {}
     
      class [[Foo]] {}
    ]] 2 errors
     
    test waring on unused class [[
      class Main {
        public static void main(String[] args) {
          System.out.println(1);
        }
      }
     
      class Foo {}
     
      class [[Foobar]] {
        Foo x;
      }
    ]] 1 warning
