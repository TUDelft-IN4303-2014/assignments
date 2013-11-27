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

### Cyclic Inheritance

### Subtyping

