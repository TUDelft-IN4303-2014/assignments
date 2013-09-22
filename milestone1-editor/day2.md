# Day 2: Syntax Analysis

In this lab, you define the concrete and abstract syntax of MiniJava in Spoofax. 
From this definition, you generate an Eclipse editor, that provides syntax checking and highlighting.

## Overview

### Objectives

Develop a syntax definition for MiniJava in SDF2 or SDF3 and generate an Eclipse editor from it. 
The definition should include
* a lexical syntax definition for
** sort `ID`,
** sort `INT`,
** whitespaces,
** single line comments and
** nested block comments,
* a context-free syntax definition or templates for sorts
** `Program`,
** `MainClass`,
** `ClassDecl`,
** `VarDecl`,
** `MethodDecl`,
** `Type`,
** `Statement` and
** `Exp`,
* constructor names for context-free syntax rules, and
* disambiguation rules.

For grading, it is required to comply with these sort names literally.

### Submission

You need to submit your test project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your syntax definition. 
The deadline for submission is October 2, 17:59.

### Grading

You can earn up to 80 points for your concrete syntax definition.
Therefor, we run 333 test cases against your implementation. 
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* lexical syntax (20 points)
** `ID` (6 points)
** `INT` (1 point)
** whitespace (3 points)
** simple comments (5 points)
** complex comments, including nested block comments (5 points)
* context-free syntax (30 points)
** `Program` (1 point)
** `MainClass` (2 points)
** `ClassDecl` (2 points)
** `VarDecl` (2 points)
** `Type` (2 points)
** `MethodDecl` (5 points)
** `Statement` (6 points)
** `Exp` (10 points)
* disambiguation (30 points)
** associativity (10 points)
** precedence (20 points)

You can earn up to 20 points for your abstract syntax definition.
Therefor, we generate an abstract syntax tree for an example program.
We inspect your syntax definition and the generated AST manually.
We particular focus on 
 meaningful constructor names, 
 distinctness of different constructs,
 and the representation of formal and actual parameter lists.

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
