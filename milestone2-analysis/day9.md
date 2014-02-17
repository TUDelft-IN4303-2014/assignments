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

You need to submit your MiniJava project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your NaBL and Stratego files.
The deadline for submission is December 4, 17:59.

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
* constraints (40 points)
    * hiding variables and fields (5 points)
    * method overloading and method overriding (20 points)
    * cyclic inheritance (10 points)
    * subtyping in assignments, return expressions, method calls (5 points)
* challenge (using TS) (5 points)

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

### Update Library

You should create the file `lib/runtime/task/new-messages.str` with the following content:

		module runtime/task/new-messages
		
		imports
		  
		  runtime/task/core
		  runtime/editor/origins
		  runtime/task/messages
		  
		signature
		
		  sorts
		  
		    Message
		    MessageTrigger
		    
		  constructors
		    
		    Failure  : List(Result) -> MessageTrigger // No results
		    Success  : List(Result) -> MessageTrigger // At least one result
		    Multiple : List(Result) -> MessageTrigger // More than one result
		
		    Message  : MessageTrigger * Term -> Instruction
		
		rules // task creation
		  
		  task-create-message-on-triggers(|partition, triggers):
		    message -> <new-task(|partition)> Message(triggers, message)
		  
		  task-create-error-on-triggers(|partition, triggers, message) = 
		    task-error-message(|message); task-create-message-on-triggers(|partition, triggers)
		    
		  task-create-warning-on-triggers(|partition, triggers, message) =
		    task-warning-message(|message); task-create-message-on-triggers(|partition, triggers)
		    
		  task-create-note-on-triggers(|partition, triggers, message) =
		    task-note-message(|message); task-create-message-on-triggers(|partition, triggers)
		  
		rules // task execution
		  
		  task-is-combinator = ?Message(_, _)
		
		  perform-task(|n):
		    Message(trigger*, message) -> []
		    where
		      <map(?Failure([]) + ?Success([_|_]) + ?Multiple([_, _|_]))> trigger*
		    where
		      task-add-message(|n, message)

The new strategy `task-create-error-on-triggers(|ctx, triggers, msg)` and its variants will give you more control when you want to create errors, warning, or notes. You should pass it a list of `triggers`.
There are three kinds of triggers:

* `Failure(t)` requires that task `t` has no result.
* `Success(t)` requires that task `t` has at least one result.
* `Multiple(t)` requires that task `t` has at least two results.

The message `msg` will only be shown if the requirements of all triggers are fullfilled.

### Update Spoofax

When you are using TS, you should update Spoofax.

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
      	<not(is-list)> ty1
      ; <not(is-list)> ty2
      where
        t := <type-match(|ctx, ty1)> ty2
 
You might notice that this is a pure refactoring, which should not affect the functionality of your code.
Finally, you should add the following boilerplate code for handling lists:

    create-subtype-task(|ctx):
      (SubTyping(), t1*, t2*) -> <type-is(|ctx, [m])> t*
      where 
         t* := <zip(create-subtype-task(|ctx, SubTyping()))> (t1*, t2*)
      <+ t* := <map-with-index(create-subtype-task(|ctx, SubTyping(), t2*))> t1*
       ; l  := <new-task(|ctx)> Length(t2*)
       ; m  := <type-match(|ctx, <length> t1*)> l 
     
    create-subtype-task(|ctx, op):
      (t1, t2) -> <create-subtype-task(|ctx)> (op, t1, t2)
     
    create-subtype-task(|ctx, op, t*):
      (i, t) -> st
      where
        t' := <new-task(|ctx)> Index(i, t*)
      ; st := <create-subtype-task(|ctx)> (op, t, t')

When you are using TS, you can achieve the same by replacing equivalence checks of the form 

     ty1 == ty2 

by subtyping checks 

     ty1 <: ty2

or

     ty1* <list: ty2*
     
where `ty1*` and `ty2*` are lists.

You then need to define the relation `<:`:

    ty1 <: ty2
    where ty1 == ty2

You should also add the following boilerplate code:

    create-subtype-task(|ctx):
      ("<list:", t1*, t2*) -> <type-is(|ctx, [m])> t*
      where 
         t* := <zip(create-subtype-task(|ctx, "<:"))> (t1*, t2*)
      <+ t* := <map-with-index(create-subtype-task(|ctx, "<:", t2*))> t1*
       ; l  := <new-task(|ctx)> Length(t2*)
       ; m  := <type-match(|ctx, <length> t1*)> l 
     
    create-subtype-task(|ctx, op):
      (t1, t2) -> <create-subtype-task(|ctx)> (op, t1, t2)
     
    create-subtype-task(|ctx, op, t*):
      (i, t) -> st
      where
        t' := <new-task(|ctx)> Index(i, t*)
      ; st := <create-subtype-task(|ctx)> (op, t, t')


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
         <not(is-list)> ty1
       ; <not(is-list)> ty2
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

#### Ancestor Classes

So far you only consider parent classes in subtyping checks. 
To get correct subtyping checks, you need to consider all ancestor classes.
This can be achieved by assigning multiple types to class names.
Reconsider the following example:

    class Main {
      public static void main ( String [] a ) {
        System.out.println(42);
      }
    }
     
    class A  {}
     
    class B extends A {}
     
    class C extends B {}

`A` should be of type `Top()`, `B` should be of types `ClassType("A")` and `Top()`, and `C` should be of types `ClassType("B")`, `ClassType("A")` and `Top()`.

To achieve this, you need to extend your `create-type-task` rule for `extends` parts with a second type, which is looked up from the parent class:

    create-type-task(|ctx):
      ... -> ty
      where
        ty1 := ... // your initial type
      ; ty2 := ... // type lookup from parent class
      ; ty  := <new-task(|ctx)> Combine([ty1, ty2])
        
There is currently no way to express this rule in TS.

### Cyclic Inheritance

Finally, you should implement constraints for cyclic inheritance.
These constraints can be expressed based on the type of `extends` parts.



