# Day 4: Simple Term Rewriting

This lab is your first encounter with Stratego.
You add a desugaring transformation and an outline view to an initial editor provided by us. 

## Overview

### Initial Editor Project

For this lab and the following milestone 2, we provide you with an initial editor project.
This project is a common starting point for all of you.
It includes 
* a parse table `assignment1/MiniJava.tbl` which passes all syntax tests,
* a corresponding signature `assignment1/MiniJava.str` and a
* pretty-printing definition `assignment1/pp.rtree`.

### Objectives

1. Define rewrite rules `desugar` which desugar
  * unary expressions into terms of the form `Uop(op, exp)`,
  * binary expressions into terms of the form `Bop(op, exp1, exp2)` and
  * octal numbers into decimal numbers.
2. Integrate `desugar` into a strategy `desugar-all` which desugars subtrees in an AST.
3. Specify rewrite rules `to-outline-label` which map AST nodes to labels in an outline view. You should include:
  * classes (class name and, if available, parent class name),
  * fields (field name and type),
  * methods (method name, parameter types, return type) and
  * local variables (variable name and type).

For grading, it is required to comply with all rule and strategy names literally.

### Submission

You need to submit your MiniJava project on Blackboard. 
As part of your submission, we ask you to provide a short paragraph explaining some choices you made in your implementation. 
The deadline for submission is October 16, 17:59.

### Grading

You can earn up to 60 points for your desugarings:

* signature (25 points)
* rewrite rules (20 points)
* strategy (10 points)
* octals  (5 points)
	
## Detailed Instructions

### Initial Editor Project

#### Signature

Signatures declare sorts and constructors for terms. 
In Spoofax, terms are used to represent abstract syntax trees. 
The corresponding signature is generated from the constructors in a syntax definition. 
You can find a signature for MiniJava in `assignment1/MiniJava.str`. 
It was generated from a syntax definition, which itself is not included in the initial project.
If you write your own syntax definition, the generated signature can be found in `include/<LanguageName>.str`.


### Desugaring

A uniform representation of unary and binary expressions eases static analysis and code generation. 
To get such a uniform representation, you need to desugar abstract syntax trees during the analysis phase.

#### Signature

Before you can implement a desugaring, you need to define a signature for the uniform representation of expressions:

1. Identify unary and binary expressions in MiniJava. 
A unary expression has one subexpression and an operator.
A binary expression has two subexpressions and an operator. 
There are more than one kind of unary expressions and more than three kinds of binary expressions in MiniJava.
2. Specify new constants for unary and binary operators in a signature. 
Use `UnOp` and `BinOp` as types of these operators.
3. Define constructors `UnExp` and `BinExp`, 
which combine an operator and an expression (respectively two expressions) to an expression.

#### Rewrite Rules

The following rewrite rule defines a rule to desugar an addition:

    rules 

      desugar: Add(e1, e2) -> BinExp(Plus(), e1, e2)

This rewrite rule is named `desugar`. 
On the left-hand side, the rule matches an addition. 
During the match, variables `e1` and `e2` are bound to actual terms. 
On the right-hand side, the rule instantiates a binary expression (in a uniform representation). 
During the instantiation, variables `e1` and `e2` are replaced with the terms they are bound to.
You can extend `desugar` to replace the different unary and binary expressions in the abstract syntax tree 
with a uniform representation of these expressions. 
Define a rewrite rule `desugar` for every unary or binary operator, which transforms the original expression into a uniform representation.

#### Naming Conventions

In Stratego, we use the following naming conventions:

* constructor and sort names: camel case, starting with an upper case (e.g. `Add`, `BinExp`)
* rule names, strategy names, variable names: lower case, multiple words seperated by `-` (e.g. `e1`, `project-path`)

#### Editor Integration

To test your transformation, you need to define a builder. 
This is done similar to the builder for pretty-printing. Add the following rewrite rule:

  editor-desugar:
    (selected, position, ast, path, project-path) -> (filename, text)
    where
      filename := <guarantee-extension(|"desugared.aterm")> path ;
      text     := <desugar> selected

This rule follows Spoofax' convention for strategies which implement editor services. 
On the left-hand site, it matches a tuple of 

* the `selected` node, 
* its `position` in the `ast`, 
* the `path` of the current file and 
* the `project path`. 

On the right-hand site, it instantiates a pair, consisting of a `filename` and the designated `text` of the file. 
Both variables are bound in the `where` clause. 
The file name is derived from the path of the current file, 
while the content of the file is a desugared version of the selected AST node.
You also need to hook your strategy into the editor, making desugaring available in the *Transform* menu. 
You can do this in `editor/MiniJava-Builders.esv`:

    builder : "Desugar AST (selection)" = editor-desugar (openeditor) (realtime) (meta) (source)

This rule defines 

* a builder, 
* its label in the *Transform* menu, and 
* its implementation strategy `editor-desugar`. 

Annotations can be used for different variants of builders:

* `(openeditor)` ensures that a new editor window is opened for the result. 
* `(realtime)` requires this editor to be updated whenever the content in the original editor changes. 
* `(meta)` restricts the builder to be only available to the language engineer, but not to the language user. 
While you can invoke the builder, people who install your MiniJava plugin cannot. 
* Finally, `(source)` tells Spoofax to run the builder on an unanalysed (and also not desugared) AST.

#### Strategies

Rewrite rules typically define local transformations inside an AST. 
Rewrite rules with the same name define a strategy of this name. 
Furthermore, strategies can be defined to orchestrate rewrite rules to complex transformations of complete ASTs. 
A strategy consists of a name and a definition, which is typically a combination of strategy applications. 
For example, the following strategy orchestrates local desugarings to a desugaring of complete ASTs:

    strategies

      desugar-all = innermost(desugar)

This strategy is named `desugar-all`. 
It applies local `desugar` rules. 
The application is guided by a generic traversal strategy `innermost`, 
which tries to apply its parameter inside a tree, starting at the leaves (bottom-up, left-to-right). 
Whenever an application is successful, the result is traversed again. 

Same results can be achieved with different generic traversals. 
You should try different traversals:

* `desugar-all = innermost(desugar)`
* `desugar-all = topdown(desugar)`
* `desugar-all = topdown(try(desugar))`
* `desugar-all = bottomup(desugar)`
* `desugar-all = bottomup(try(desugar))`
* `desugar-all = alltd(desugar)`

Try to understand what is going on and decide for a suitable one.
You can use the library strategy `debug` to print the currently visited node. 
For example, `innermost(debug; desugar)` will `debug` all nodes before it tries to `desugar` them. 

#### Editor Integration Revisited

With a builder, you can invoke a transformation manually from the *Transform* menu. 
However, desugaring should be an automatic transformation as part of static analysis. 
In Spoofax, static analysis is performed by strategies. 
In the initial project, these strategies are implemented in `trans/minijava.str`:

    rules // Analysis
  
      editor-analyze = analysis-default-editor
  
      analysis-single-default-interface = 
        analysis-single-default(id, id, id|<language>)
      analysis-multiple-default-interface = 
        analysis-multiple-default(parse-file <+ !(), id, id, id|<language>, <project-path>)

The strategies `analysis-single-default` and `analysis-multiple-default` require various strategy parameters.
The first parameter in `analysis-single-default` and 
the second parameter in `analysis-multiple-default` are used for transformations before name analysis.
This is the place, where you should hook-in your desugaring:

    rules // Analysis
  
      editor-analyze = analysis-default-editor
  
      analysis-single-default-interface = 
        analysis-single-default(desugar-all, id, id|<language>)
      analysis-multiple-default-interface = 
        analysis-multiple-default(parse-file <+ !(), desugar-all, id, id|<language>, <project-path>)

To test your implementation, you can use a predefined builder labeled *Show analyzed syntax*. 
Open a MiniJava program and run the builder. 
At this point, you can get rid of your old desugaring builder.

#### Challenge

Challenges are meant to distinguish excellent solutions from good solutions. 
Typically, they are less guided and require more investigation or higher programming skills. 

Define a desugaring for octal numbers. 
In Java, octal numbers start with leading zeros. 
Define a rewrite rule which matches such numbers and transforms them to decimal integers. 
See the [API docs](http://releases.strategoxt.org/docs/api/libstratego-lib/stable/docs/) for useful helper strategies.

### Outline View

