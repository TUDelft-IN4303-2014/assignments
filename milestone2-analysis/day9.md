# Day 9: Inheritance and Subtyping

In this lab, you extend name binding and typing rules for MiniJava in order to support inheritance and subtyping.

## Overview


## Detailed Instructions

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
The following strategies from previous assignments might be useful:

* `nabl-lookup-local(|ctx)`
* `nabl-lookup-local-import(|ctx)`
* `type-lookup(|ctx)`
* `type-match(|ctx)`
* `task-create-error-on-success(|ctx, task, msg)`
* `task-create-error-on-failure(|ctx, task, msg)`
* `task-create-note-on-success(|ctx, task, msg)`
* `task-create-note-on-failure(|ctx, task, msg)`

### Subtyping

Your current typing constraints check for type equivalence. 
However, subtyping is allowed in the following places:

1. In an assignment, the type of the assigned expression needs to be a subtype of the type of the variable.
2. In a method declaration, the type of the return expression needs to be a subtype of the declared method type.
3. In a method call, the types of the actual parameters need to be subtypes of the types of the formal parameters.
4. In method overriding, the return type of the overriding method needs to be a subtype of the return type of the overridden method.

You can achieve subtyping in three steps.
In the first step, you prepare constraints for subtyping checks.
In the second step, you consider only parent classes for subtyping.
Finally, you consider ancestor classes for subtyping.

#### Subtyping Constraints

You should factor out equivalence checks from your constraints.
First, you need to define a relation for subtyping:

    signature constructors
     
      SubTyping: Relation

Next, you should identify code fragments of the form

    <type-match(|ctx, ty1)> ty2

and replace them with

    <create-subtype-task(|ctx)> (SubTyping(), ty1, ty2)

where `ty1` should be a subtype of `ty2`.
You should do this only in places, where subtyping is actually allowed.
Finally, you can define `create-subtype-task`:

    create-subtype-task(|ctx):
      (SubTyping(), ty1, ty2) -> t
      where
        t := <type-match(|ctx, ty1)> ty2
 
You might notice that this is a pure refactoring, which should not affect the functionality of your code.

When you are using TS, you can achieve the same by replacing equivalence checks of the form 

     ty1 == ty2 

by subtyping checks 

     ty1 <: ty2

You then need to define the relation `<:`:

    ty1 <: ty2
    where ty1 == ty2

#### Parent Classes

The general idea is to assign types to the `extends` part of a class declaration and to class names.
Consider the following example:

    class Main {
      public static void main ( String [] a ) {
        System.out.println(42);
      }
    }
     
    class A  {}
     
    class B extends A {}
     
    class C extends B {}

`A` should be of type `Top()`, `B` should be of type `ClassType("A")`, and `C` should be of type `ClassType("B")`.

First, you need to define a constructor for a top type `Top()`.
Such a top type is common for type systems with subtyping.
In Java, this corresponds to `java.lang.Object`, which is a supertype of all class types.
When a class extends another class, the type of the `extends` part should be the class type of the parent class.
Otherwise, the `extends` part is missing, but it is still represented in the abstract syntax.
Its type should be `Top()`.
You can specify these types either in `create-type-task` rules or in TS.
Before you continue, you should check your type definitions by inspecting the analysed syntax and the corresponding tasks.

Now you can assign types to class names in NaBL rules.
The idea is, that the type of a class name is the class type of its parent class or `Top()`, if it has no parent.
You can do this with a `where` clause which retrieves the type of the `extends` part. 
You can test your specification by building the project and hovering over a class name.

In a next step, you have to extend `create-subtype-task`.
Currently, it only checks for type equivalence.
You can now add a second check, which checks if `ty1` is a class type of some class with a parent type `ty2`:

     create-subtype-task(|ctx):
       (SubTyping(), ty1, ty2) -> t
       where
         check1 := <type-match(|ctx, ty1)> ty2 ;
         c-name := ... // extract class name from class type with a rewrite task
         ty     := ... // lookup type from class name
         check2 := ... // match ty with ty2
         t      := <new-task(|ctx)> Choice([check1, check2])
         
In TS, you need to extend the definition of the `<:` relation:

    ty1 <: ty2
    where ty1 == ty2
       or ty1 => ... // extract class name from class type with a pattern match
      and ...        // lookup type from class name
      and ...        // match type with ty2 
