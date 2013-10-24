# Day 6: Name Analysis

In this lab, you define name bindings and corresponding constraints for MiniJava. 
From this definition, you generate an Eclipse editor, that provides 
  name resolution, content completion, and constraint checking.

## Overview

### Objectives

Specify name analysis for MiniJava in NaBL and Stratego and generate an Eclipse editor from it. 
The specification should include:

1. Name binding rules for
  * class declarations,
  * class references,
  * method declarations,
  * field declarations,
  * variable declarations and
  * variable and field references.
2. Scoping rules for 
  * class declarations,
  * method declarations,
  * field declarations and
  * variable declarations.
3. Typing rules for
  * main class declarations,
  * field declarations and
  * variable declarations.
4. Custom constraint rules for
  * duplicate definitions,
  * unresolved references,
  * variable declarations which hide local field declarations and
  * references to the main class.

### Submission

You need to submit your MiniJava project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your NaBL and Stratego files.
The deadline for submission is November 13, 17:59.

### Grading

You can earn up to 70 points for the correctness of your name analysis.
Therefor, we run several test cases against your implementation. 
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (20 points)
  * class declarations and references (6 points)
  * method declarations (2 point)
  * variable and field declarations (6 points)
  * variable and field references (6 points)
* scopes (10 points)
* constraints (35 points)
  * duplicate definitions (10 points)
  * unresolved references (10 points)
  * hiding variables (5 points)
  * main class references (10 points)
* challenge (5 points)

You can earn up to 15 points for your messages in errors and warnings.
We particular focus on 
 readibility, 
 precision, 
 and the level of detail in your messages.

### Spoofax Update

This lab requires you to update Spoofax to the latest unstable release.

1. Choose *Install New Software* from the *Help* menu.
2. Add `http://download.spoofax.org/update/unstable/` as an update site.
3. Install Spoofax. Eclipse will tell you it is already installed and suggests an update. This is fine.

## Detailed Instructions
