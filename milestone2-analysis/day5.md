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
  * errors on fields hiding fields,
  * warnings on variables hiding fields,
  * errors on instantiating, subclassing, or referencing the main class.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment5` on GitHub. 
Your GitHub repository contains a step-by-step procedure how to file such a request. 
Test files created during this lab should go into a new project `MiniJava-tests-names`.
This project should contain a `README.md` with a short paragraph explaining the organisation of your test project. 
The deadline for submission is October 22, 17:59.

### Grading

You can earn up to 5 points for the organisation of your test project.
We take its structure, file names, test names, and your explanation into account.
You can earn up to 95 points for the coverage of your test cases.
**Note:** It is important to name tests in a testsuite uniquely. Tests in different test suites can have the same name, but for grading, we need to be able to distinguish tests in the same test suite by their name.

### Early Feedback

We provide early feedback for the effectiveness of your test cases.
This feedback gives you an indication which parts of the name binding rules might still be uncovered by your tests. 
It includes a summary on how many erroneous language definitions you reveal and how many points you earn by detecting them.
We start sending out early feedback on Monday.

## Detailed Instructions

### Testing Reference Resolution

In test cases for reference resolution, 
 you write syntactically correct programs and
 mark names at definition and use sites with inner square bracket blocks. 
You can then relate the use site with the definition site in a `resolve`-`to` clause, 
  using numbers to refer to the inner blocks. 
For example, the following two test cases require to resolve the type `Foo` to the name in the definition of class `Foo`:

    module resolution
     
    language MiniJava
    start symbol Program
     
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
 
You can use `setup` headers and footers to avoid repeating parts in similar test cases.

You should come up with test cases for the resolution of class names, field names, parameter names, and variable names.
Start with simple test cases, but keep in mind that coverage is the main criterion for your grade. 
It is important to think about 
 forward and backward references,
 resolution in the presence of homonyms, 
 and the influence of class hierarchies on resolution.
 
### Testing Error Checking

In test cases for error checking, you need o specify the number of errors, warnings, or notes in a test case 
 in `errors`, `warnings`, or `notes` clauses. 
You can use inner square bracket blocks to mark the positions where messages should be reported. 
For example, the following test cases specify a correct MiniJava program, 
 a program with two errors which are reported on the name of a duplicate class `Foo`, 
 and another program with a warning which is reported on the name of an unused class `Foobar`:
 
    module resolution
     
    language MiniJava
    start symbol Program
     
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
     
      class Foo {}
     
      class Foo {}
    ]] 2 errors
     
    test warning on unused class [[
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

You can start with test cases for duplicate definitions, missing definitions, and unused definitions.
Similar to your syntax test cases, you can pair up positive (`0 errors`) and negative test cases.
For duplicate definitions, each definition with the same name should produce an error, which is why the `error on duplicate class` test above expects 2 errors.

Next, you should develop test cases for 
 cyclic inheritance, 
 hiding fields and variables, 
 and main class instantiation/subclassing.
Again, you should keep in mind that coverage is the main criterion for your grade. 
