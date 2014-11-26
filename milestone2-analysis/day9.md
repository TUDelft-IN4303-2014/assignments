# Day 9: Inheritance and Subtyping

In this lab, you extend name binding and typing rules for MiniJava in order to support inheritance and subtyping.

## Overview

### Objectives

Specify inheritance and subtyping in MiniJava with name binding and type rules in NaBL, TS and Stratego and generate an Eclipse editor from it. The specification should include:

* Name binding rules for inheritance.
* Typing rules for 
    * `extends` parts of class declarations,
    * class declarations and
    * a subtype relation.
* Custom constraint rules for 
    * variables hiding inherited fields,
    * fields hiding inherited fields,
    * method overloading,
    * method overriding,
    * cyclic inheritance and
    * valid subtyping in assignments, method calls and return expressions.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment9` on GitHub. 
Your GitHub repository contains a step-by-step procedure how to file such a request. 
This project should contain a `README.md` with a short paragraph explaining the organisation of your NaBL, TS, and Stratego files.

The deadline for submissions is December 3rd, 17:59.

### Grading

You can earn up to 75 points for the correctness of your name and type analysis.
Therefor, we run several test cases against your implementation. 
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (5 points)
    * inheritance (5 points) 
* subtyping (25 points)
    * parent types (15 points)
    * ancestor types (10 points)
* constraints (45 points)
    * hiding variables and fields (10 points)
    * method overloading and method overriding (20 points)
    * cyclic inheritance (10 points)
    * subtyping in assignments, return expressions, method calls (5 points)

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
We will consider the fact that these languages are new to you.

## Detailed Instructions

### Spoofax Update

This lab requires you to update Spoofax to the latest version.

1. Choose *Check for Updates* from the *Help* menu.
2. Wait for updates to be found.
3. Install the updates to Spoofax.
4. Restart Eclipse

### Inheritance

Your current implementation does not consider inheritance.
Field names are resolved to field declarations in the surrounding class, but not to declarations in ancestor classes.
Similarly, method names are resolved to method declarations in the callee class, but not to declarations in its ancestors.

You can fix this by importing fields and methods from parent classes into their child classes with an `imports` clause.
A clause

    imports NS1, NS2 from NS name
    
works like

    refers to NS name
    
but additionally imports declarations of `NS1` and `NS2` from the referred scope into the current scope.
It only considers declarations which are declared in the referred scope, but not those which are imported into the referred scope.
`imported` is a keyword for one specific label, not a clause such as `imports`.
The following clause also imports declarations of `NS1` which are imported into the referred scope:

    imports NS1, imported NS1, NS2 from NS name

### Hiding Variables and Fields

You can now extend your constraints for hidden fields.
For this purpose, you can use `nabl-lookup-local-import(|ctx)`.
This strategy works similar to `nabl-lookup-local(|ctx)`.
It creates a task to lookup a name in the current context,
but only considers declarations, which are imported into the current scope.
It does not consider declarations from surrounding scopes.
You should add checks for variable and field declarations, which hide fields from ancestor classes.
You should give warnings on variable declarations and report errors on field declarations.

### Method Overloading and Overriding

Next, you can specify constraints for overloaded and overridden method declarations.
You should report errors on overloading methods and give notes on overriding methods.
The following strategies might be useful:

* `nabl-lookup-local(|ctx)`
* `nabl-lookup-lexical(|ctx)`
* `nabl-lookup-local-import(|ctx)`
* `nabl-lookup-lexical-import(|ctx)`
* `type-lookup(|ctx)`
* `type-match(|ctx)`
* `task-create-error-on-triggers(|ctx, triggers, msg)`
* `task-create-warning-on-triggers(|ctx, triggers, msg)`
* `task-create-note-on-triggers(|ctx, triggers, msg)`

### Subtyping

Your current typing constraints check for type equivalence. 
However, subtyping is allowed in the following places:

1. In an assignment, the type of the assigned expression needs to be a subtype of the type of the variable.
2. In a method declaration, the type of the return expression needs to be a subtype of the declared method type.
3. In a method call, the types of the actual parameters need to be subtypes of the types of the formal parameters.
4. In method overriding, the return type of the overriding method needs to be a subtype of the return type of the overridden method.

You can achieve subtyping in three steps:

1. define and instantiate subtyping relations in TS.
2. make existing constraints work with subtyping where needed.
3. add new constraints to handle subtyping errors.

#### Defining the Subtype Relation

Relations in TS are used to model subtyping. A relation in TS is much like the regular definition of a logical relation, it is a set of binary term tuples that are in a certain relation with each other. For example, `class B extends A` indicates that `B` has a subtype relation with `A`. Relations allow matching such as: is class `B` in a subtyping relation with `A`?, which will be used to implement constraints later.

First, a relation and its properties must be defined

```
relations

  define property1, property2, ... <name:
```

The available properties for relations are:

* reflexive: If elements in the relation relate to them self.
* transitive: If `A` relates to `B`, and `B` relates to `C`, then `A` also relates `C`.
* symmetric: If `A` relates to `B`, then `B` relates to `A`.

A relation must be named so it can be used in other places. Note that name can also be empty, so `<:` can be used as relation name. You should define a subtyping relation and think about which properties it should have.

Now you can store new instances of subtype relations using regular type rules.

```
type rules

  pattern :-
  where store c <name: sc
```

This stores a new instance of a relation between `c` and `sc`. Add corresponding rule(s) to store instances of subtype relations.

#### Using the Subtype Relation

You can use the subtype relation in TS in place of equality checks. Checks in the form of

```
ty1 == ty2 
```

can be replaced by subtyping checks 

```
ty1 <name: ty2
```

In Stratego constraints, such checks can be done using

```
match := <relation-create-match(|ctx)> ("<name:", ty1, ty2)
```

which does the same as the TS variant.

Now you can update any constraints with subtyping checks where needed, and create new constraints to handle subtyping errors. 
