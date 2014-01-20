# Day 3: Syntactic Editor Services

In this lab, you add a pretty-printer and syntactic editor services to your MiniJava editor.

## Overview

### Objectives

1. Extend generated folding rules with hand-written rules. 
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
  
In all of these folders you will find files which were generated from your syntax definition. 
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
* `include/MiniJava-parenthesize.str`: strategies to add parentheses to an AST according to the priorities of your language.
* `include/minijava.ctree` and/or `include/minijava.jar`: compiled Stratego code of your language.

### Preliminaries

Before you start with the actual assignment, you should make sure that

1. your syntax definition provides all start symbols needed by your test cases,
2. your context-free syntax is specified in `templates` sections by productions of the form

        Sort             = <...>
        Sort.Constructor = <...>
        Sort             = [...]
        Sort.Constructor = [...]

3. your start symbol is set to Program so that your editor accepts only complete MiniJava programs. 

### Folding Rules

Presentational editor services such as code folding and syntax highlighting are defined 
in `.esv` files in the `editor` folder.
When you build your editor and open a MiniJava file, you can see that the editor provides already some folding points.
These points are syntax-dependent and are specified by folding rules in `editor/MiniJava-Folding.generated.esv`.
All editor services follow the same basic structure: 
they have a derived file (`EntityLang-Folding.generated.esv`) 
and a custom definition file (`EntityLang-Folding.esv`). 
The first file is automatically generated from your syntax definition, each time the project is built.
You can use the second file to customize the editor. 

Folding rules indicate which syntactic constructs can be folded, and take one of the following forms:

    <Sort>.<Constructor>
    <Sort>._
    _.<Constructor>

The `(folded)` annotation can be used for constructs that should be folded automatically:

    _.Imports (folded)

Spoofax uses heuristics to automatically derive folding rules, 
 based on the logical nesting structure in the syntax of the language. 
Currently, it derives folding rules from productions rules with a lexical identifier and child elements.
While not perfect, the heuristics provide a good starting point for a new folding definition. 
Any undesired definitions in the generated file can be disabled 
 by using the `(disabled)` annotation in a custom rule:

    Definition._ (disabled)

You should now specify additional custom folding rules. 
It is important, to include only those structures, where folding is reasonable. 

### Completion Templates

Syntactic content completion provides users with completion suggestions based purely on static, syntactic templates. 
For example

    completion template Statement : "while(true) {}" =
      "while(" <true:Exp> ") " <{}:Statement> (blank)  

is a syntactic completion template for `while` loops. 
Such templates are composed of static strings and placeholder expressions. 
Static strings allow for precise control of the presentation and are enclosed by double quotes. 
They can use `\n` for newlines or `\t` for one indentation level (following the user’s tab/space configuration). 
Placeholder expressions are indicated by angular brackets. 
The editor automatically moves the cursor to these expressions once the user selects a completion proposal, 
allowing the expressions to be filled in as the user continues typing.

Spoofax generates completion templates from `templates` sections in your syntax definition.
You can find these templates in `editor/<name>.generated.esv`files. 
Until now, these completion templates are not integrated into your editor.
You can change this, by __importing all the generated esv files in__ `editor/MiniJava-Completions.esv`.
After building your project, you can test completion by pressing `Ctrl + Space`.

Typically, your completion templates lack proper indentation and line breaks. 
You can fix this by improving your templates in the syntax definition.
The completion templates follow the indentation and line breaks from the syntax definition.
You can hide particular placeholders from completion templates with a `hide` option or 
change a placeholder label with a `text="label"` option.
Read the [SDF3 documentation](http://metaborg.org/wiki/sdf) on templates and placeholders for more information.

You should improve your syntax definition in order to get readable completion templates with a consistent indentation.
You might read on [indent styles](http://en.wikipedia.org/wiki/Indent_style) for some inspiration.
Finally, you should specify few additional completion templates manually.
This might involve larger code patterns or useful variants of the generated templates.

### Pretty-Printing

Spoofax also generates pretty-printing rules from your syntax definition.
You can find these rules in `trans/<name>.pp.generated.str`.
You need to define a builder and a corresponding build strategy 
  to hook a pretty-printing option into the *Transform* menu of your MiniJava editor.
You define builders in `editor/MiniJava-Builders.esv`:

    builder : "Pretty-print" = pp-builder (source) (openeditor)                                              

This builder definition adds a new entry to the *Transform* menu.
The left-hand side of the definition specifies the label used in the menu.
The right-hand side indicates a Stratego strategy which implements the corresponding action.
The `(source)` annotation specifies that the strategy is applied to the original, un-desugared, un-analyzed AST.
The `(openeditor)` annotation specifies that the action opens an editor with the result.

You still need to implement the strategy `pp-builder` which is responsible
for performing the action of the *Pretty-print* menu entry.
You can do this in a new file `trans/pp.str`:

    module pp
    
    imports
      libstratego-gpp
      lib/runtime/tmpl/pp
      include/MiniJava-parenthesize
      ...

This defines a module `pp` which imports Stratego's generic pretty-print library `libstratego-gpp` 
and a module `MiniJava-parenthesize`.
The latter is generated from your syntax definition and provides strategies to add parentheses to an AST.
These strategies obey the priority rules of your syntax definition.
__You also need to import all the generated__ `*.pp.generated.str` __files here__.
These generated files contain errors, but they can be safely ignored. 

Now, you can define `pp-builder`:

    rules
	
     	pp-builder:
        (selected, position, ast, path, project-path) -> (name, content)
        with
          name    := <guarantee-extension(|"pp.mjv")> path
        ; ast'    := <parenthesize-MiniJava> ast
        ; box     := V([], <prettyprint-Program> ast')
        ; content := <box2text-string(|100)> box

This rule follows a fixed interface for interoperability with the editor. 
The left-hand side of the rule is a tuple of
* the `selected` node,
* its `position` in the tree,
* the complete `ast` of the file, 
* the file `path` and 
* the `project-path`.

The right-hand side specifies a file which should be changed (or created) by
* its `name` and
* its `content`.

The `with` clause specifies how to bind these variables. 
The output file `name` is derived from the `path` of the file opened in the editor.
Its `content` is derived in three steps:

1. We add parentheses to the  `ast` of the input file, which results in a new `ast'`.
2. We pretty-print `ast'` with the generated `prettyprint-Program` strategy. 
   This results in some nested *boxes*, which we surround with a vertical (`V`) `box`.
   These boxes are a fundamental concept of the generic pretty-print library.
3. We turn the `box` into text with a maximal width of `100`.
   This results in the `content` of our output file.

In order to test the pretty-print builder, you need to import `pp.str` from `trans/minijava.str` and build your project.
Create or open a `.mjv` test file with a valid program, press the down-facing arrow on the right of the Transform button and choose `Pretty-print`.
This will apply `pp-builder` to the current file and show the result in a new editor.
