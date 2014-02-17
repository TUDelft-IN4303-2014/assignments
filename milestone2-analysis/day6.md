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
Therefore, we run several test cases against your implementation. 
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
 readability, 
 precision, 
 and the level of detail in your messages.

Finally, you can earn up to 5 points for the organisation of your NaBL and Stratego files and 
up to 10 points for the quality of your code.
We focus on
  readability in general,
  meaningful variable names and
  the consistent use of NaBL Stratego paradigms.
We will consider the fact that both languages are new to you.

### Spoofax Update

This lab requires you to update Spoofax to the latest unstable release and 
  to fix an import in the milestone we provided you.

1. Choose *Install New Software* from the *Help* menu.
2. Add `http://download.spoofax.org/update/unstable/` as an update site.
3. Install Spoofax. Eclipse will tell you it is already installed and suggests an update. This is fine.
4. Delete the directory `lib/runtime` in your existing MiniJava project.
5. Update the import in `trans/minijava.str` from `lib/runtime/nbl/-` to `lib/runtime/nabl/-`.
6. Build your MiniJava project.

## Detailed Instructions

### Name Binding

In Spoofax, name bindings are specified in NaBL.
NaBL stands for *Name Binding Language* and the acronym is pronounced 'enable'.
Name binding is specified in terms of
  namespaces, 
  binding instances (name declarations), 
  bound instances (name references),
  scopes and
  imports.

To start a new name binding specification, 
  you need to create a `.nab` file in directory `trans` or one of its subdirectories.

    module module-name
      
    imports
      assignment1/MiniJava

The module name has to be the same as the file name and should include the path relative to the `trans` directory.
For example, a file `foo.nab` in directory `trans/bar` should define a module `bar/foo`.
When you save an NaBL file, a corresponding Stratego file will be generated from it.
This file contains implementation strategies for your name analysis.
You need to import this file into `trans/minijava.str`.
When you build your project, your name binding rules become effective in your MiniJava editor.

#### Class Names

We use class names as an example to get you started with NaBL.
First, you start a `namespaces` section and define a namespace `Class`:

    namespaces 
      
      Class

In NaBL, a *namespace* is a collection of names and is not necessarily connected to a specific language concept.
Different concepts can contribute names to a single namespace. 
For example, in MiniJava variables and parameters contribute to the same namespace. 

Once you have defined a namespace, 
you can define name bindings rules in a `binding rules` section:

    binding rules
     
      Class(c, _, _, _): defines Class c
       
      ClassType(c): refers to Class c

Each binding rule is of the form `pattern : clause*`, 
  where `pattern` is a term pattern (like in Stratego rewrite rules) and 
  `clause*` is a list of name binding specifications about the language construct that is matched by `pattern`. 
For example, the first rule specifies binding instances of class names.
Its pattern matches regular MiniJava classes (but not the main class).
Its `defines` clause states that these classes are definition sites for class names (i.e. they bind class names).
Similarly, the second rule specifies bound instances of class names.
Its pattern matches class types and 
its `refers` clause states that such class types are use sites for class names (i.e. they refer to bound class names).
When a name can refer to different namespaces, you can combine refer clauses with `otherwise`, e.g. 

    Foo(name) : refers to Namespace1 name otherwise refers to Namespace2 name

When you save the file and build your project, 
  you will get reference resolution for class names in your MiniJava editor.
You might recognise that reference resolution works across files in the same Eclipse project.
Though this is a nice feature, this is incorrect for MiniJava,
 where files scope their class declarations.
You can specify this scoping behaviour in a binding rule:

    Program(_, _): scopes Class
    
The pattern of this rule matches programs and its `scopes` clause specifies that this is a scope for class names.

#### More Name Binding Rules

You are now able to complete the name binding rules for MiniJava. You should come up with:

1. more name binding rules for class names (there might be more definition and use sites of class names),
2. namespaces and name binding rules for method declarations, field declarations and variable declarations,
3. scoping rules for these declarations and
4. name binding rules for variable and field references.

### Custom Constraints

NaBL provides generic checks for duplicate definitions and unresolved references.
In order to give specific error messages, you need to replace them with your own constraints.
First, you need to create a new Stratego file in `trans` or one of its subdirectories:

    module module-name
     
    imports
      
      assignment1/MiniJava
      runtime/nabl/-
      runtime/task/-
      runtime/types/-

Again, the module name has to be the same as the file name and should include the path relative to the `trans` directory.
Now you can turn off the generic checks:

    strategies
     
      nabl-check-disable-duplicate(|uri, ns) = id
      nabl-check-disable-hiding(|uri, ns) = id
      nabl-check-disable-unresolved = id
      
#### Unresolved References

You can create your own constraints by providing rewrite rules for `nabl-constraint(|ctx)`:

    rules
     
      nabl-constraint(|ctx):
        ClassType(c) -> <fail>
        where
          <has-annotation(?Use(task))> c
        ; msg  := "Your meaningful error message should be here"
        ; <task-create-error-on-failure(|ctx, task, msg)> c
        
This rule matches a language construct on the left-hand side (in this case a class type) and fails on the right-hand side.
The failure ensures that you can report different errors on the same language construct.
The shown rule reports an error on unresolved class names in class types.
Errors can be reported on failing tasks.
In the current example of an unresolved error message, this needs to be a failing resolution task.
The `where` clause of the rule obtains this `task` from the class name by matching a particular annotation.
Next, an error message `msg` is created.
You should replace this with a meaningful message.
You can use string interpolation to include elements from the matched term in the error message.
Finally, a library strategy is called to create an error in case of a failing task.
The arguments to this strategy are

1. a context variable `ctx`,
2. the `task` which needs to fail to trigger the error message and
3. the error message `msg`.

The strategy is applied to the term where the error should be shown (in this case, the class name).
You should follow this pattern to provide custom error messages for all unresolved class references,
field references and variable references.

#### Duplicates and Hiding

Constraints on duplicate or hiding declarations can also be specified with `nabl-constraint(|ctx)` rules.
However, in these cases you cannot match existing tasks but need to create your own ones.
The following library strategies might be useful:

* `nabl-lookup-local(|ctx)` creates a resolution task that searches for a name in the same (local) scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical(|ctx)` creates a resolution task that searches for a name in the local and surrounding (lexical) scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical(|ctx, ns)` does the same as above, but considers names in namespace `ns`.
  You need to pass a term for the namespace of interest here. Constructor for such terms are generated from your
  name binding specification.
* `task-create-error-on-success(|ctx, task, msg)` creates an error message `msg`, if `task` has one or more solutions.
* `task-create-error-on-multiple(|ctx, task, msg)` creates an error message `msg`, if `task` has more than one solution.
* `task-create-warning-on-failure`, `task-create-warning-on-success` and `task-create-warning-on-multiple` are variants for warnings instead of errors.

### Properties on Definitions

#### Defining Properties

You can define certain properties of binding instances.
`type` is a built-in property and you should define the type of field and variable declarations.
You can do so in a special `defines` clause:

    ...: defines Field f of type t
    
This associates the type `t` with the field name `f`.
You can see such associations in the index (try the *Show index (current)* builder).
Search for entries of the form `Prop(URI(...), Type(), Result(...)`.
The URI specifies the definition by naming its scopes.
The result refers to a task which has the property as a result.
You can see tasks and their results with the *Show tasks (current)* builder.
The index stores all definitions and properties of these definitions.

#### Property-based Constraints 

You can use properties in constraints. 
For example, you can define a constructor for a special type and associate the main class with this type.
You can use this to check if a class reference refers to the main class:

    nabl-constraint(|ctx):
      ClassType(c) -> <fail>
      where
        lookup := <type-lookup(|ctx)> c
      ; match  := <type-match(|ctx, YourSpecialTypeConstructor())> lookup
      ; msg    := "Another fancy error message"
      ; <task-create-error-on-success(|ctx, match, msg)> c
      
This rule reports errors on class types which refer to the main class.
It creates a task `lookup` which looks up the type of `c` and
another task `match` which matches the result of this lookup against your special type.
If this match is successful, an error is reported.
You should define your own special type constructor and provide a meaningful error message.
Next, you can do similar checks for main class subtyping and main class instantiation.

### Challenges

Challenges are meant to distinguish excellent solutions from good solutions. 
Typically, they are less guided and require more investigation or higher programming skills.

1. Enhance your error messages with information about scopes.
   For example, you might want to mention the surrounding method of a variable which hides a field.
   You can retrieve such information from the annotations of a binding instance.
   To retrieve annotations, you should use strategies from the [standard library](http://releases.strategoxt.org/docs/api/libstratego-lib/stable/docs/).
   To analyse the anotated URIs, you should use strategies from `lib/runtime/nabl/uri`.

2. In a post-analysis desugaring, replace `VarRef(x)` terms which refer to fields and not to variables with a term `FieldRef(x)`.
