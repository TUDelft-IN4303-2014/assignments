# Installation

##### *How can I disable the cygwin warnings about paths when building on Windows?*

1. Add a system [environment variable](http://superuser.com/questions/284342/what-are-path-and-other-environment-variables-and-how-can-i-set-or-use-them) 
   called `CYGWIN` with the value `nodosfilewarning`. 
2. Restart eclipse.

# Testing Syntax Definition

##### *How do I get started?*

1. Create a General/Project in Eclipse. 
2. Create a new file with the `.spt` extension. 
3. Write your test module in that file. If you have built the MiniJava project, the spt files will be parsed. 

Each test module gets its own spt file. You can add directories to your project to group the test files. 

# Syntax Definition

##### *Should I use templates?*

Yes. 

##### *Is it possible to split my syntax definition up in several files possibly in subfolders?*

Yes that is possible. You can have the following directory tree for example:

    syntax/
      subdir/
        c.sdf
        c.sdf3
      b.sdf
      b.sdf3
      MyLanguage.sdf
      MyLanguage.sdf3

MyLanguage.sdf3 should have the following imports if it depends on b and c:

    module MyLanguage 
    imports b subdir/c
    ...

and c should be declared as

  module subdir/c
    ...

If you get errors while you think you shouldn't, do the following:

1. Delete all .sdf files
2. Restart Eclipse
3. Open all .sdf3 files and save them (you have to make a change first in Eclipse) so that the .sdf files are regenerated
4. Hope for the best

##### *The {bracket} attribute seems to have no effect, how come?*

You probably added a constructor name to that particular grammar rule which makes no sense if you think about it. 

##### *I added something in a .sdf3 file but it does not seem to have any effect but I did not check the generated .sdf file to see if what I added has actually been incorporated, what do I do?*

You check the .sdf file. 

##### *I see no errors in my context-free priorities section but they do not appear in the associated .sdf file, what could be the problem?*

You probably made a typo. The priorities section does not show an error (as of september 2013) or warning message if a constructor is not defined. 

# Syntactic Editor Services

# Simple Term Rewriting
