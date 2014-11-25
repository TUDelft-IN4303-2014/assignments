# Day 8: Type Analysis

In this lab, you define typing rules and constraints for MiniJava. 
From this definition, you generate an Eclipse editor, that provides type information in hover help and type constraint checking.

## Overview

### Objectives

Specify type analysis for MiniJava in NaBL, TS, and Stratego, and generate an Eclipse editor from it. 
The specification should include:

1. Name binding rules for
  * `this` expressions,
  * method declarations and
  * method calls.
2. Typing rules for
  * integer and boolean literals,
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

You need to submit your MiniJava project with a pull request against branch `assignment8` on GitHub. 
Your GitHub repository contains a step-by-step procedure how to file such a request. 
This project should contain a `README.md` with a short paragraph explaining the organisation of your NaBL, TS, and Stratego files.

The deadline for submissions is November 26th, 17:59.

### Grading

You can earn up to 75 points for the correctness of your type analysis.
Therefore, we run several test cases against your implementation. 
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
  * `this` expression (2 points)
  * variable and field references (2 points)
  * method call (5 points)
* constraints (30 points)
  * expressions (10 points)
  * statements and method declarations (20 points)
* challenge (5 points)

You can earn up to 10 points for your messages in errors and warnings.
We particular focus on 
 readability, 
 precision, 
 and the level of detail in your messages.

Finally, you can earn up to 5 points for the organisation of your NaBL, TS and Stratego files and 
up to 10 points for the quality of your code.
We focus on
  readability in general,
  meaningful variable names and
  the consistent use of NaBL Stratego paradigms.
We will consider the fact that both languages are new to you.

## Detailed Instructions

### TS

In this lab you specify typing rules and constraints in TS, a brand new metalanguage for specifying type systems of programming languages.
TS has a number of rough edges:

1. Imports of Stratego files (such as desugared signatures) will show errors, you can ignore these.
2. Generated Stratego files can contain errors, but compile fine.
3. Generated variable names change in each compilation.
4. The language lacks syntactic sugar for typical typing rule patterns.

Despite these issues, the language allows you to specify typing rules and constraints in a declarative and concise way.
TS files end in `.ts` and generate corresponding `.generated.str` files, __be sure to import these modules in your minijava.str file__.
There overall structure looks like this:

```
module types

imports
  
  common/src-gen/signatures/MiniJava-sig
  common/desugar
     
type rules
  
  ...
```

### Tasks

To support incremental name and type analysis, we use tasks to specify name and type analysis.
You have seen tasks already in the assignment on name analysis.

#### Idea

A task is a unit of computation, which might depend on index entries (such as definitions or properties) or on the results of other tasks.
Tasks are collected in a traversal, before they are evaluated.
Results of evaluated tasks are cached.
When a file is changed, tasks are only re-collected for this file.
A task is only re-evaluated, if it is new or if one of its dependencies changed.
This might include tasks which originated from a different file than the file that changed.

#### Building

Name and type tasks are executed incrementally based on changes made in MiniJava programs. 
However, tasks do not take changes of your language specification into account.
If you encounter an unexpected result after changing your naming or typing rules, right click the project containing your example files, and choose `Spoofax -> Reload analysis data`.

#### Debugging

Hovers offer a quick way to check if type analysis works as expected. When you move your mouse over an expression or definition in a MiniJava editor, you should see its type in a hover text.

Your test cases from the previous lab should enable a more principled way of testing type analysis. 
Tests that expect a class type as a result have to be modified to accept annotations on the name of the class. For example, an expectation `ClassType("A")` needs to be changed to `ClassType("A"{_})`.

When you get unexpected results, you can inspect index entries and 
  collected tasks with the *Show analysis*, *Show tasks* and *Show index* builders.
Probably the most useful builders for you are those which show them only for the current file (also called a partition) or for a selection.

Consider the following task entry:

```
task 1 [2, 3] =
  produce &2
  ->
  [Int()]
```
      
This entry consists of

* an identifier `1`,
* a list `[2, 3]` of dependencies on other tasks,
* an instruction `produce &2` which refers the result of task `2`,
* and a list of results `[Int()]`.

You can navigate the dependencies of tasks to find the reasons for failing tasks.
The following instructions exist:

* `resolve ns n in s* wrt props []` resolves a name `n` of namespace `ns` in scopes `s*`.
* `choose x <+ y` chooses from alternatives `x` and `y` deterministically. 
  If `x` is a term or a task with a result, `x` is chosen.
  Otherwise, `y` is chosen.
* `concat t1 + t2` combines the results of tasks `t1` and `t2`.
* `Message(tsk, [], result, trm, msg)` shows an error message `msg` on term `trm` based on the `result` (either succeeding, failing, or producing multiple results) of task `tsk`.
  There are similar variants for warnings and other conditions such as the success of a task.
* `produce x` produces result `x`.
* `CollectDefs(x)` retrieves the definition of a reference `x`.
* `lookup Type() props on d` looks up the type of a definition `d`.
* `check Type() prop t1 against t2 wrt Eq()` checks if types `t1` and `t2` are equal.
* `rewrite t wrt s` rewrites term `t` using strategy `s`.

### Typing Rules

Typing rules specify the types of expressions.

#### Literals

In the simplest case, the type of an expression is directly known.
An typical example for such expressions are literals.
For example, integer literals are of type `int`:

    IntValue(v): Int()

Note that you cannot use wildcards in TS patterns.

#### Unary and Binary Expressions

In more complicated cases, you need to check the types of subexpressions.
The general idea is that only well-typed expressions have a type.
For example, `1 + true` should have no type, since the right subexpression is of type `boolean` instead of type `int`.
In TS, the general pattern for checking subexpressions is this:

    e: ty
    where e1: ety1
      and ety1 == ty1
      and e2: ety2
      and ety2 == ty2

In TS, you need to specify the types of operators in axioms:

    Length(): (IntArray(), Int())

Here, `e` should match an expression with subexpressions `e1` and `e2` and
`ty` should be the type of `e`, while `ty1` and `ty2` should be the expected types of `e1` and `e2`, respectively.

You can follow this pattern for the rules for unary and binary expressions.
You should define the following rules:

1. One rule for `UnExp`.
2. One rule for `BinExp`.
3. A rule for each unary and binary operator, which rewrites the operator to a tuple.
   This tuple should consist of the expected types of subexpressions and the type of the operator itself.
   For example, the following rule states that `Length` requires a subexpression of type `int[]` and yields an expression of type `int`:   
``` 
Length(): (IntArray(), Int())
```

The array subscript expression also requires a type, similar to a binary expression.

The signatures for desugared expressions are missing from the intial project, so here they are for reference:

```
signature
  constructors
   	
    BinExp      : BinOp * Exp * Exp -> Exp
    UnExp       : UnOp * Exp -> Exp
    Plus        : BinOp
    Minus       : BinOp
    Times       : BinOp
    LtOp        : BinOp
    AndOp       : BinOp
    LengthOp    : UnOp
    NewArrayOp  : UnOp
    NotOp       : UnOp
```

#### References

For references, you need to lookup the type of the corresponding definition.
Types of definitions are specified in NaBL rules.
You should have specified these types in the name analysis assignment:

    ...: defines Field f of type t

In TS, you can lookup the type of definitions as follows:

    e: ...
    where definition of r: ty

Here, `r` should be a reference in an expression `e`.

### Name Binding Revisited

#### `this` Expressions

To type `this` expressions, you first need to specify name binding rules for `this`.
Since `this` is nowhere specified explicitly, 
  you need to add an implicit definition clause to one of the existing name binding rules.
An implicit definition clause has the following form:

    ...: implicitly defines Namespace name
    
You might want to reuse an existing namespace or define a new one for `this`.
If you use a new namespace, you need to scope this namespace properly.
As a name, you should use the constructor for `this`.
You can find this constructor in the abstract syntax of MiniJava.
Implicit definitions can also have properties, which allows you to specify the type of `this`.

Next, you should specify a rule which resolves `this` to the implicit definition you just added.
Again, you should use the constructor for `this` as the name in this rule.

Finally, you can define a typing rule which looks up the type of `this`. 
You need to make sure that `definition of` is applied to the term matched by the rule, not to a new term `This()`.
You can achieve this by matching with a variable `v` and a term `e` matching `this`:

    v@e: ty
    where definition of v ...

#### Method Calls

Method calls need to be resolved with respect to the type of the callee expression.
This expression needs to be of a class type and the method call should resolve to a method in the corresponding class.
Such contextual references are specified in NaBL as follows:

    ...: 
      refers to Namespace1 name1 in Namespace2 name2
      where e has type ty
      
The `where` clause requires an expression `e` to be of type `ty`.
`name1` is then resolved in the scope of `name2`. 
You need to instantiate this pattern for method calls which resolve to methods inside classes.
You should use a pattern for `ty`, which binds `name2`.

To type a method call, you need to define the type of a method name definition:

    ...: defines Method m of type t

Next, you can specify a type rule for method calls, which looks up the type of the method definition.
You should have this working, before you continue.

The current solution works nicely, but also yields a type for method calls with missing arguments, additional arguments, or wrong argument types.
To avoid this, you need to specify a more sophisticated type at the definition site.
This type should include the expected types for the parameters and the return type of the method.
You can construct this sophisticated type either as a tuple of parameter types and return type 
or define a special constructor for this type.
Similar to the rule for method calls, you can collect the parameter types in a `where` clause.
To make this work, you also need to create type rules for parameters:

    Param(t, p): ...
    
Before you continue, you should check if the type associated with the method name carries all the information you need.
Do not continue, until this is working.

Next, you can adopt your typing rule for method calls.
The type lookup will now yield the more sophisticated type.
You need to extract the parameter types and the return type for further tasks.
You can use a pattern for `sophisticated-type` which matches `parameter-types` and `return-type`:

    ...: ...
    where definition of m: sophisticated-type // lookup sophisticated type and 
      and sophisticated-type : ...           // match parameter-types and return-type
      and  ...                                // check actual argument types w.r.t. parameter types

### Constraints

In the assignment on name analysis, you have seen already strategies to specify constraints.
You can specify type constraints directly in TS.

#### Expressions

In the typing rules for expressions, 
  you check the types of subexpressions against expected types.
These checks will fail, if an actual type does not match an expected type.
In TS, you can specify errors and warnings in `else` clauses:

```
e : t
where e1: et1
  and et1 == t1
 else error "Your meaningful error message should be here" on e1
  and e2: et2
  and et2 == t2
 else error "Another meaningful error message should be here" on e2
```

#### Statements

Statements typically do not have a type, but they might expect a certain type of an expression in the statement.
For example, an if statement requires a boolean expression.
In TS, you can specify such constraints in special rules:

```
s :-
where e: t
 else error "Yet another meaningful error message should be here" on e
```
        
#### Method Declarations

Finally, you can also specify a constraint which checks the type of a return expression against the declared return type.

#### Superfluous and Cascading Type Errors

In the current version of TS, there is an issue with superfluous and cascading type errors. Whenever a type check (such as `e1 == e2`) fails, any subsequent type checks and errors attached to those type checks will also fail. The term will also be untyped, meaning that any other type rules that depend on the type of that term will also fail.

To work around this issue, types rules should be split up into multiple rules. The example fragment for expressions should be split up in the following way:

```
// Always produce a type, to prevent cascading.
e : t
where ... : t

// First constraint
e :-
where e1: et1
  and et1 == t1
 else error "Your meaningful error message should be here" on e1

// Second constraint, prevent superfluous error when first constraint fails.
e :-
where e2: et2
  and et2 == t2
 else error "Another meaningful error message should be here" on e2
```
