# Day 2: Syntax Analysis

In this lab, you define the concrete and abstract syntax of MiniJava in Spoofax. 
From this definition, you generate an Eclipse editor, that provides syntax checking and highlighting.

## Overview

### Objectives

Develop a syntax definition for MiniJava in SDF3 and generate an Eclipse editor from it. 
The definition should include:

1. A lexical syntax definition for
  * sort `ID`,
  * sort `INT`,
  * whitespaces,
  * single line comments and
  * nested block comments.
2. A context-free syntax definition or templates for sorts
  * `Program`,
  * `MainClass`,
  * `ClassDecl`,
  * `VarDecl`,
  * `MethodDecl`,
  * `Type`,
  * `Statement` and
  * `Exp`.
3. Constructor names for context-free syntax rules.
4. Disambiguation rules.

For grading, it is required to comply with all sort names literally.

### Submission

You need to submit your MiniJava project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your syntax definition.
The deadline for submission is October 2, 17:59.

### Grading

You can earn up to 80 points for your concrete syntax definition.
Therefor, we run 333 test cases against your implementation. 
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* lexical syntax (20 points)
  * `ID` (6 points)
  * `INT` (1 point)
  * whitespace (3 points)
  * simple comments (5 points)
  * complex comments, including nested block comments (5 points)
* context-free syntax (30 points)
  * `Program` (1 point)
  * `MainClass` (2 points)
  * `ClassDecl` (2 points)
  * `VarDecl` (2 points)
  * `Type` (2 points)
  * `MethodDecl` (5 points)
  * `Statement` (6 points)
  * `Exp` (10 points)
* disambiguation (30 points)
  * associativity (10 points)
  * precedence (20 points)

You can earn up to 20 points for your abstract syntax definition.
Therefor, we generate an abstract syntax tree for an example program.
We inspect your syntax definition and the generated AST manually.
We particular focus on 
 meaningful constructor names, 
 distinctness of different constructs,
 and the representation of formal and actual parameter lists.

## Detailed Instructions

### Agile Software Language Engineering

Spoofax supports short development cycles.
This enables you build the syntax definition step by step.
In each step, you focus on a single aspect, 
 for example a particular nonterminal symbol or a particular grammar rule.
After each step, you can check your progress by building the project and running your test cases. 
This requires you to declare the same start symbols in your syntax definition as in your test suites. 

You can also test the generated editor in the same Eclipse instance.
This requires you to specify the start symbol that is used by the editor
  in the main editor description `editor/MiniJava.main.esv`.
Of course, this should also be a start symbol in your syntax definition.
You can test the editor by opening a MiniJava program.  

You can also use **Show AST** in the editor's **Transform** menu to test your abstract syntax definition interactively.
While you change a MiniJava program in the editor, its corresponding AST is updated automatically.
You might notice that the editor will give you an AST even for syntactically incorrect programs.
This is because generated Spoofax editor support syntactic error recovery. 

![Menu entry to inspect the abstract syntax of a program fragment.](http://strategoxt.org/pub/Spoofax/Features/show-abstract-syntax.png  "Menu entry to inspect the abstract syntax of a program fragment.")

### SDF3

You should define your syntax in SDF3.
This requires you to update Spoofax to the latest unstable release.
Afterwards, you can start by creating a new file `syntax/MiniJava.sdf3`:

    module MiniJava
    
    context-free start-symbols 
    
When you save this file, you should get a corresponding file `syntax/MiniJava.sdf`.
You can also split your syntax definition over several modules in `syntax/`. 
Do only import modules that you wrote yourself.
