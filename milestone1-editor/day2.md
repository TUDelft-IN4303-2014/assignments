# Day 2: Syntax Analysis

In this lab, you define the concrete and abstract syntax of MiniJava in Spoofax. 
From this definition, you generate an Eclipse editor, that provides syntax checking and highlighting.

## Overview

### Objectives


### Submission

You need to submit your test project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your syntax definition. 
The deadline for submission is October 2, 17:59.

### Grading

You can earn up to 5 points for the organisation of your syntax definition. 
We take file names, organisation of rules, comments, and your explanation into account. 
You can earn up to 95 points for the effectiveness of your test cases. 
Therefor, we run your test cases against 184 correct and erroneous implementations of MiniJava. 
You earn points, when your test cases reveal certain kinds of errors in erroneous implementations while passing correct implementations. 
The total number of points depends only on how many kinds of errors you reveal, not on the number of test cases that reveal these errors.

## Detailed Instructions

### Testing the Syntax Definition

You should build the syntax definition step by step. 
After each step, you can check your progress by building the project and running your test cases. 
Therefor, you need to declare the same start symbols in your syntax definition as in your test suites. 
To also test your MiniJava editor interactively, 
  you need to specify the start symbols also in the main editor description `editor/MiniJava.main.esv`. 
You can also use **Show AST** in the editor's **Transform** menu to test your abstract syntax definition interactively.

![Menu entry to inspect the abstract syntax of a program fragment.]
(http://strategoxt.org/pub/Spoofax/Features/show-abstract-syntax.png 
"Menu entry to inspect the abstract syntax of a program fragment.")

### Lexical Syntax

As a syntax definition formalism, you can choose either SDF or the Template Language.
