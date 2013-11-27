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

### Method Overriding and Overloading

### Cyclic Inheritance

### Subtyping

