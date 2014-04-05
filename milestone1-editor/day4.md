# Day 4: Simple Term Rewriting

This lab is your first encounter with Stratego.
You add an outline view and a desugaring transformation to an initial editor provided by us. 

## Overview

### Initial Editor Project

For this lab and the following milestone 2, we provide you with an initial editor project.
This project is a common starting point for all of you.
It includes 
* a parse table `assignment1/MiniJava.tbl` which passes all syntax tests,
* a corresponding signature `assignment1/MiniJava.str` and a
* pretty-printing definition `assignment1/pp.rtree`.

### Objectives

1. Specify rewrite rules `to-outline-label` which map AST nodes to labels in an outline view. You should include:
  * classes (class name and, if available, parent class name),
  * fields (field name and type),
  * methods (method name, parameter types, return type) and
  * local variables (variable name and type).
2. Define rewrite rules `desugar` which desugar
  * unary expressions into terms of the form `Uop(op, exp)`,
  * binary expressions into terms of the form `Bop(op, exp1, exp2)` and
  * octal numbers into decimal numbers.
3. Integrate `desugar` into a strategy `desugar-all` which desugars subtrees in an AST.

For grading, it is required to comply with all rule and strategy names literally.

### Submission

You need to submit your MiniJava project on Blackboard. 
As part of your submission, we ask you to provide a short paragraph explaining some choices you made in your implementation. 
The deadline for submission is October 16, 17:59.

### Grading

You can earn up to 40 points for your outline view:

* names in labels (8 points)
* super classes in labels (2 points)
* types in labels (25 points)
* challenge (5 points)

You can earn up to 60 points for your desugarings:

* signature (25 points)
* rewrite rules (20 points)
* strategy (10 points)
* challenge  (5 points)
	
## Detailed Instructions

### Initial Editor Project

1. Download the [initial project](https://github.com/guwac/compiler-construction/releases/download/ms1/MiniJava.zip).
2. Import the project into your workspace:
    1. right-click into the Package Explorer
    2. select **Import...** from the context menu
    3. choose **General/Existing Projects into Workspace** from the list
    4. select **archive file** (2nd choice) as a source
    5. select the downloaded MiniJava project
    6. press the **Finish** button
3. Build the project:
    1. select the project folder
    2. select **Build Project** from the **Project** menu
    3. the console will report success or failure

#### Signature

Signatures declare sorts and constructors for terms. 
In Spoofax, terms are used to represent abstract syntax trees. 
The corresponding signature is generated from the constructors in a syntax definition. 
You can find a signature for MiniJava in `assignment1/MiniJava.str`. 
It was generated from a syntax definition, which itself is not included in the initial project.
If you write your own syntax definition, the generated signature can be found in `include/<LanguageName>.str`.

### Outline View

#### Rewrite Rules

An outline view can be specified by rewrite rules `to-outline-label` in `trans/editor/outline.str`.
These rules should rewrite AST nodes to their label in an outline view.
For example, the following rule rewrites a variable declaration to its name, which will be used as a label.

    rules
      
      to-outline-label: Var(t, v) -> v

On the left-hand side, the rule matches a variable declaration. 
During the match, variables `t` and `v` are bound to actual terms. 
On the right-hand side, the rule instantiates a label. 
During the instantiation, variable `v` is replaced with the term it is bound to.
You can extend `to-outline-label` to provide labels for 

* class declarations, 
* field declarations and
* method declarations.

When you build the project and open a MiniJava file, you will get an outline of this program in the outline view.
In case you do not see any outline view, you can select it in *Show View* from Eclipse's *Window* menu.

#### Naming Conventions

In Stratego, we use the following naming conventions:

* constructor and sort names: camel case, starting with an upper case (e.g. `Add`, `BinExp`)
* rule names, strategy names, variable names: lower case, multiple words separated by `-` (e.g. `e1`, `project-path`)

#### String Interpolation

In many cases, you want to provide more information than just the name.
For example, you might want to show not only a variable's name, but also it's type.
The following rule achieves this:

    to-outline-label: 
      Var(t, v) -> label
      where
        t'    := <pp> t
      ; label := <concat-strings> [v, ": ", t']
    
On its right-hand side, it produces a `label`, which is bound in the `where` clause.
First, the term bound to `t` is turned into a string bound to `t'` by applying a strategy `pp`.
You need to define this strategy yourself by a couple of rewrite rules:

    rules
      
      pp: Bool()       -> ...
      pp: ClassType(c) -> ...
      pp: Int()        -> ...
      pp: IntArray()   -> ...

Next, the label is bound to the concatenation of 
the string bound to `v`, 
a constant string `": "`, 
and the string bound to `t'`.

String concatenation is not very intuitive.
Instead, you can also use string interpolation:

    to-outline-label: 
      Var(t, v) -> $[[v]: [t']] 
      where 
        t' := <pp> t
      
String interpolation allows you to combine text with variables.
Text is enclosed in `$[` and `]`, while variables inside the text are enclosed in `[` and `]`.
These variables need to be bound to strings.

You should provide the following information in your outline labels:

* class name and super class name
* field name and type
* method name, parameter types (not parameter names), return type
* variable name and type

For parameter types, you need to turn a list of parameters into a string.
You can do this with a recursive strategy:

    pp-params: // empty parameter list
      [] -> ...
    
    pp-params: // single parameter
      [Param(t, p)] -> ...
      
    pp-params: // at least two parameters
      [Param(t, p), param | params] -> ...
      where
        // do something on first parameter
        ...
        // recursive call on remaining parameters
        ... := <pp-params> [param | params]
        
Your current outline view is missing a root node.
You can add a root node by providing a label for programs.

#### Annotations

In Stratego, terms can be annotated with additional information.
The Spoofax outline view uses annotations to determine the icon of a node.
You can specify the icon to use in an annotation:

    to-outline-label: 
      Var(t, v) -> label{icon} 
      where 
        t'    := <pp> t
      ; label := $[[v]: [t']]
      ; icon  := "icons/var.gif"

We do not require you to use icons and you will not earn any points with them.
If you want to use them anyway, you should put the icons into the folder `icons`
and place a proper attribution or license file next to them.

#### Challenge

Challenges are meant to distinguish excellent solutions from good solutions. 
Typically, they are less guided and require more investigation or higher programming skills. 

1. Provide the file name as the root node label. 
See `lib/runtime/editor/origins/` for a suitable strategy.
2. Outline the main method as a subnode of the main class.
This requires you to drop the import `editor/MiniJava-Outliner.generated`.
Instead, you need to define a strategy 

        outline = custom-label-outline(to-outline-label, to-outline-node)
    Visit `lib/runtime/editor/outline-library` for inspiration.

### Desugaring

A uniform representation of unary and binary expressions eases static analysis and code generation. 
To get such a uniform representation, you need to desugar abstract syntax trees during the analysis phase.

#### Signature

Before you can implement a desugaring, 
you need to define a signature for the uniform representation of expressions in `trans/analysis/desugar.str`:

1. Identify unary and binary expressions in MiniJava. 
A unary expression has one subexpression and an operator.
A binary expression has two subexpressions and an operator. 
There are more than one kind of unary expressions and more than three kinds of binary expressions in MiniJava.
2. Specify new constants for unary and binary operators in a signature. 
Use `UnOp` and `BinOp` as types of these operators. 
Again, you should use names based on the semantics of an operator, not on its syntax.
Reading an expression aloud might help you to find suitable constructor names.
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
Define a rewrite rule `desugar` in `trans/analysis/desugar.str` for every unary or binary operator, 
which transforms the original expression into a uniform representation.

#### Editor Integration

To test your transformation, you need to define a builder. 
This is done similar to the builder for pretty-printing. Add the following rewrite rule to `trans/editor/builders.str`:

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
You should try different traversals in `trans/analysis/desugar.str`:

* `desugar-all = innermost(desugar)`
* `desugar-all = topdown(desugar)`
* `desugar-all = topdown(try(desugar))`
* `desugar-all = bottomup(desugar)`
* `desugar-all = bottomup(try(desugar))`
* `desugar-all = alltd(desugar)`

Try to understand what is going on and decide for a suitable one.
You can use the library strategy `debug` to print the currently visited node. 
For example, `innermost(debug; desugar)` will `debug` all nodes before it tries to `desugar` them. 

For the submission, you need to prepare an explanation (1 paragraph) of 
1. the choice you made,
2. why this choice is suitable for this project, and
3. why other choices would be less suitable.

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

Define a desugaring for octal numbers. 
In Java, octal numbers start with leading zeros. 
Define a rewrite rule which matches such numbers and transforms them to decimal integers. 
See the [API docs](http://releases.strategoxt.org/docs/api/libstratego-lib/stable/docs/) for useful helper strategies.

