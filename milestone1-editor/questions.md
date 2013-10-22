# Installation

##### How can I disable the cygwin warnings about paths when building on Windows?

1. Add a system [environment variable](http://superuser.com/questions/284342/what-are-path-and-other-environment-variables-and-how-can-i-set-or-use-them) 
   called `CYGWIN` with the value `nodosfilewarning`. 
2. Restart eclipse.

# Testing Syntax Definition

##### How do I get started?

1. Create a Spoofax editor project ([instructions](https://github.com/guwac/compiler-construction/blob/master/assignments/milestone1-editor/overview.md#initial-spoofax-project)) 
   and a test project ([instructions](https://github.com/guwac/compiler-construction/blob/master/assignments/milestone1-editor/overview.md#initial-test-project)) in Eclipse.
2. Create a new test suite in the test project. 
   1. Select *File > New > File* from the menu.
   2. Use `.spt` as a file extension.
3. Write your first test module in that file. 
   
Each test module gets its own file. 
You can add directories to your project to group the test files. 
If you build the editor project, your test modules will be parsed. 

# Syntax Definition

##### Should I use templates?

Yes. 
This will save you several hours of work in the next assignment.

##### Is it possible to split my syntax definition up in several files possibly in subfolders?

Yes that is possible. You can have the following directory tree for example:

    syntax/
      subdir/
        c.sdf
        c.sdf3
      b.sdf
      b.sdf3
      MyLanguage.sdf
      MyLanguage.sdf3

`MyLanguage.sdf3` should have the following imports if it depends on `b` and `c`:

    module MyLanguage
     
    imports 
      b 
      subdir/c
    
and `c` should be declared as

    module subdir/c
    
If you get errors while you think you shouldn't, do the following:

1. Delete all `.sdf` files.
2. Open all `.sdf3` files and run *Generate SDF (concrete, for selection)* from the *Transform* button.
   If this does not result in a corresponding `.sdf` file, something is wrong in your `.sdf3` file.

##### Why does the `bracket` attribute has no effect?

You probably used a constructor name together with the `bracket` attribute.
This is a mistake.

##### Why are my `context-free priorities` not translated into SDF2?

There is an error in these priorities, which causes the generator to fail.
You probably made a typo in a constructor name. 
The priorities section does not show errors on undefined constructor names. 

# Syntactic Editor Services

# Simple Term Rewriting
