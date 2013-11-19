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
