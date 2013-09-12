# Day 1: Testing Syntax Analysis

In this lab, you develop a test suite for syntax analysis.
The test suite consists of positive and negative test cases.

## Overview

### Objectives

Develop a test suite for syntax analysis.
The test suite should provide

1. Syntactically valid and invalid test cases for sorts
  * Program,
  * MainClass,
  * ClassDecl,
  * VarDecl,
  * MethodDecl,
  * Type,
  * Statement,
  * Exp, 
  * ID and
  * INT.
2. Abstract syntax tests for valid instances of these sorts.
3. Disambiguation tests for associativity and precedence in expressions.
4. Test cases for mandatory and optional whitespace.

### Submission

You need to submit your test project on Blackboard.
As part of your submission, we ask you to provide a short paragraph explaining the organisation of your test project.
The deadline for submission is September 25, 17:59.

### Grading

You can earn up to 5 points for the organisation of your test project.
We take its structure, file names, test names, and your explanation into account.
You can earn up to 95 points for the effectiveness of your test cases.
Therefor, we run your test cases against 184 correct and erroneous implementations of MiniJava.
You earn points, 
  when your test cases reveal certain kinds of errors in erroneous implementations
  while passing correct implementations.
The total number of points depends only on how many kinds of errors you reveal, 
  not on the number of test cases that reveal these errors.
