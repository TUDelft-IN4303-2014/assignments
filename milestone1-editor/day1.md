# Day 1: Testing Syntax Analysis

In this lab, you develop a test suite for syntax analysis.
The test suite consists of positive and negative test cases.

## Overview

### Objectives

Develop a test suite for syntax analysis.
The test suite should provide

1. Syntactically valid and invalid test cases for sorts
  * `Program`,
  * `MainClass`,
  * `ClassDecl`,
  * `VarDecl`,
  * `MethodDecl`,
  * `Type`,
  * `Statement`,
  * `Exp`, 
  * `ID` and
  * `INT`.
For grading, it is required to comply with these sort names literally.
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
  
## Detailed Instructions

### Anatomy of a Test Suite

In Spoofax, a test suite consists of one or more `.spt` files.
Consider the following example file:

    module example
    
    language MiniJava
    start symbol INT
    
    test double digit   [[42]]  parse succeeds
    test floating point [[4.2]] parse fails

The first lines specify 
  the name of the test `module`, 
  the `language` under test, 
  and the `start symbol` used for a test. 
In this `example` module, we like to test sort `INT` of the `MiniJava` language.

The last two lines specify a positive and a negative test case. 
Each test case consists of 
  a name, 
  a code fragment in double square brackets, and 
  a condition which determines 
  what kind of test should be performed (parsing) and 
  what the expected outcome is (success or failure). 

You can run the test suite from the **Transform** menu. 
This will open the *Spoofax Test Runner View*
 which provides you information about failing and succeeding test cases in a test suite. 
You can also get instant feedback while editing a test suite.
Since you have not yet implemented the MiniJava editor, all your positive test cases fail.
You will work on this during the next lab.

### MiniJava Syntax Definition

In order to write your own test cases, you need to understand MiniJava's syntax. 
You can find the ultimate MiniJava syntax definition in the *MiniJava Language Reference Manual*.
The manual says nothing about reserved words.
But it states that the meaning of a MiniJava program is given by the meaning as a Java program. 
Therefore, you should treat all reserved words in Java as reserved in MiniJava as well and provide test cases which address this issue.
This includes not only reserved keywords, but also reserved literals.

### Test Cases

You can now start writing your own test cases.

#### Lexical and Context-free Syntax

Start with the lexical syntax and think about valid and invalid identifiers. 
Try to focus on corner cases such as 
* identifiers made of a single character,
* mixings of letters and digits (Which kinds of mixing are allowed? Which are not?),
* underscores in identifiers.

Next, you should walk through the whole syntax definition. 
Keep thinking of valid and invalid code fragments. 
Identify interesting corner cases and turn them into positive and negative test cases.

Try to keep your test cases small.
Each test case should focus on a single aspect. 
For example, a negative test case should only contain one error. 
The name of a test case should reflect the aspect it addresses.

You can organise test cases which focus on similar aspects in test modules.
Again, the name of a test module should reflect these aspects. 
Finally, you can organise test suites in folders.

#### Abstract Syntax

Next, you should focus on abstract syntax trees. 
Come up with positive test cases which specify the expected ASTs.

    test integer constant [[42]] parse to SomeFancyConstructor("42")

Think about good constructor names. 
Your ASTs should meet the following requirements:
* Similar things are represented in a similar way, e.g.
    * classes without a parent are represented similarly to classes with a parent,
    * variable references on the left-hand side and on the right-hand side of assignments should be represented the same way,
* different things are represented differently, e.g.
    * field declarations are distinguishable from variable declarations,
    * identifiers in declarations are distinguishable from identifiers in references,
* constructor names focus on the semantic of a construct, not on the syntax, e.g.
    * binary expressions are named after the operation, not after the operator symbol,
* the structure is kept simple, e.g.
    * constructors are only used where they add information,
    * method definitions and calls represent subtrees for argument lists as flat lists.

Take care if you browse SDF grammars for inspiration. 
Constructors in these grammars do not always meet these requirements.

In larger ASTs, you are typically not interested in all details. 
You can use the wildcard `_` for children that you are not interested in.
In the following example, we do not care about 
  the AST for the method type, 
  the two variable declarations, 
  the print statement,
  and the return expression:

    test AST for addition 
    [[public int m() {
      int i;
      int j;
      System.out.println(42);
      return 42;
    }
    ]] parse to AnotherFancyConstructor(_, "m", [], [_, _], [_], _)

#### Disambiguation

Next, you need to focus on disambiguation. 
Come up with positive test cases which specify the correct ASTs for nested expressions. 
As an alternative, you can also specify two fragments in concrete syntax, which should result in the same AST:

    test left associative addition [[21 + 14 + 14]] parse to [[(21 + 14) + 14]]

You can find a table listing the associativity and priorities of operators in [this book](http://introcs.cs.princeton.edu/java/11precedence/).
Do not focus only on binary expressions.

#### Layout

Finally, you can focus on layout. 
Think about places where whitespace is not optional but mandatory and define corresponding positive and negative test cases. 
Finish with test cases for single-line comments, standard block comments, and nested block comments.
Single-line comments cannot only end with a newline character, but also at the end of file.
