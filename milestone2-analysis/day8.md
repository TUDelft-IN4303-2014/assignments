# Day 8: Type Analysis

In this lab, you define typing rules and constraints for MiniJava. 
From this definition, you generate an Eclipse editor, that provides type information in hover help and type constraint checking.

## Overview

### Objectives

Specify type analysis for MiniJava in NaBL, TS and Stratego and generate an Eclipse editor from it. 
The specification should include:

1. Name binding rules for
  * `this` expressions,
  * method declarations and
  * method calls.
2. Typing rules for
  * integer and boolean constants,
  * unary and binary expressions,
  * variable and field references,
  * object creation,
  * `this` expressions,
  * and method calls.
4. Custom constraint rules for type errors in 
  * expressions,
  * statements and
  * method declarations.
  
### Submission

You need to submit your MiniJava project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your NaBL and Stratego files.
The deadline for submission is November 27, 17:59.

### Grading

You can earn up to 75 points for the correctness of your type analysis.
Therefor, we run several test cases against your implementation. 
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (15 points)
  * this expressions (5 points)
  * method declarations (4 points)
  * method calls (6 points)
* typing rules (25 points)
  * unary expressions (5 points)
  * binary expressions (10 points)
  * object creation (1 point)
  * `this` expression (2 point)
  * variable and field references (2 point)
  * method call (5 points)
* constraints (30 points)
  * expressions (10 points)
  * statements and method declarations (20 points)
* challenge (5 points)

You can earn up to 10 points for your messages in errors and warnings.
We particular focus on 
 readibility, 
 precision, 
 and the level of detail in your messages.

Finally, you can earn up to 5 points for the organisation of your NaBL, TS and Stratego files and 
up to 10 points for the quality of your code.
We focus on
  readibility in general,
  meaningful variable names and
  the consistent use of NaBL Stratego paragdims.
We will consider the fact that both languages are new to you.

### Spoofax Update

This lab requires you to update Spoofax to the latest unstable release and 
  to fix an import in the milestone we provided you.

1. Choose *Install New Software* from the *Help* menu.
2. Add `http://download.spoofax.org/update/unstable/` as an update site.
3. Install Spoofax. Eclipse will tell you it is already installed and suggests an update. This is fine.
4. Build your MiniJava project.

## Detailed Instructions

### Challenge

Challenges are meant to distinguish excellent solutions from good solutions. 
Typically, they are less guided and require more investigation or higher programming skills.

The challenge for this lab is to specify typing rules and constraints as much as possible in TS, a brand new metalanguage for specifying type systems of programming languages.
TS has a number of rough edges:

1. Generated Stratego files contain errors, but compile fine.
2. Generated variable names change in each compilation.
3. The language lacks syntactic sugar for typical typing rule patterns.

Despite these issues, the language should allow you to specify typing rules and constraints in a declarative and concise way.
TS files end in `.ts` and generate corresponding `.generated.str` files.

### Tasks

To support incremental name and type analysis, we use tasks to specify name and type analysis.
You have seen tasks already in the assignment on name analysis.

A task is a unit of computation, which might depend on index entries (such as definitions or properties) or on the results of other tasks.
Tasks are collected in a traversal, before they are evaluated.
Results of evaluated tasks are cached.
When a file is changed, tasks are only re-collected for this file.
A task is only re-eveluated, if it is new or if one of its dependencies changed.
This might include tasks which originated from a different file than the file that changed.

### Typing Rules

Typing rules specify the types of expressions.
Instead of specifying these types directly, we wrap them in tasks.
For each kind of expression, you should define a rule `create-type-task(|ctx)`,
  which rewrites an expression to a task yielding its type.
There are three strategies to create type analysis tasks:

1. `<type-is(|ctx)> ty` creates a task which has `ty` as its result.
2. `<type-lookup(|ctx)> ref` creates a task which looks up the type of a reference `ref`. 
3. `<type-match(|ctx, ty1)> ty2` creates a task which matches `ty1` against `ty2`. 

You have already used `type-lookup` and `type-match` in the name analysis lab for checking main class instantiations.

#### Typing Axioms

In the simplest case, the type of an expression is directly known.
An typical example for such expessions are constant values.
For example, integer constants are of type integer.
The following rule matches an integer constant and creates a task which will result in the integer type:

    create-type-task(|ctx): IntValue(_)  -> <type-is(|ctx)> Int()

You should specify similar rules for boolean literals and for object creations.

When you want to accept the challenge, here is the previous rule in TS:

    IntValue(_): Int()
    
