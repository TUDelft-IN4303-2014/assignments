# Day 3: Syntactic Editor Services

In this lab, you add a pretty-printer and syntactic editor services to your MiniJava editor.

## Overview

### Objectives

1. Extend generated folding patterns with hand-written patterns. 
You should include only structures, where folding is reasonable. 
2. Integrate generated pretty-printing rules and completion templates into your MiniJava editor.
Improve your syntax definition to support 
  * textual labels in these templates,
  * hiding optional placeholders,
  * multi-line templates and
  * consistent indentation.
3. Extend your editor with additional, hand-written completion templates.

### Submission

You need to submit your MiniJava project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your syntax definition.
The deadline for submission is October 9, 17:59.

### Grading

You can earn up to 10 points for your folding patterns.
We will take the overall editor experience into account and
we will deduct points for too many (and by this unusable) folding options.

You can further earn up to 70 points for your improved templates.
We will focus on readability of pretty-printed code and completion suggestions and 
on consistent indentations.

Finally, you can earn up to 20 points for hand-written completion templates.

### Spoofax Update

We fixed some bugs in SDF3.
When you want to benefit from these fixes, you should update Spoofax to the latest unstable release.

1. Choose *Install New Software* from the *Help* menu.
2. Choose `http://download.spoofax.org/update/unstable/` as an update site.
3. Install Spoofax. Eclipse will tell you it is already installed and suggests an update. This is fine.

## Detailed Instructions

### Anatomy of a Spoofax Project

Until now, you mainly worked on files in the `syntax` folder of your project. 
During this lab you will also edit files in the `editor` and `trans` folders.
So this is a good point to talk about the general structure of a Spoofax project.

First of all, every Spoofax project is an Eclipse plug-in project. 
This allows you to deploy your editor as a plugin using the Eclipse update site mechanism. 
Users do not need to have Spoofax installed for using your editor. 
You will find the typical administrative boilerplate code for such plug-ins in files 
`plugin.xml`, `build.properties`, `META-INF/MANIFEST.MF`, and `editor/java`.

The actual language definition is spread over three folders:

* `syntax` contains all syntax definition files, including the main file `MiniJava.sdf3`.
* `trans` contains all transformation files, including the main file `minijava.str`. 
* `editor` contains editor service definition files, including the main file `MiniJava.main.esv`,
  which you have seen already during the last lab.
  
In all of these folders you will find files which where generated from your syntax definition. 
For each file `<name>.sdf3`, there are generated files

* `syntax/<name>.sdf`: an SDF2 definition which is equivalent to the SDF3 definition.
* `editor/<name>.generated.esv`: completion templates derived from SDF3 templates.
* `trans/<name>.pp.generated.str`: pretty-printing strategies derived from SDF3 templates.

You can find more generated files in `editor` and `include` folders:

* `editor/MiniJava-Folding.generated.esv`: folding patterns derived from your syntax definition.
* `include/MiniJava.def`: your complete syntax definition in SDF2.
* `include/MiniJava-Permissive.def`: a permissive version of the syntax definition, which supports error recovery.
* `include/MiniJava.tbl`: the parse table of your language.
* `include/MiniJava.str`: the signature for ASTs of your language.
* `include/minijava.ctree` and/or `include/minijava.jar`: compiled Stratego code of your language.

### Folding Patterns

### Completion Templates

### Pretty-Print Builder


