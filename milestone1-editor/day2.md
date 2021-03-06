# Day 2: Syntax Definition

In this lab, you define the concrete and abstract syntax of MiniJava in Spoofax. 
From this definition, you generate an Eclipse editor, that provides syntax checking, error recovery and syntax highlighting.

## Overview

### Objectives

Develop a syntax definition for MiniJava in SDF3 and generate an Eclipse editor from it. 
The definition should include:

1. A lexical syntax definition for
  * sort `ID`,
  * sort `INT`,
  * whitespaces,
  * single line comments and
  * nested block comments.
2. A context-free syntax definition for sorts
  * `Program`,
  * `MainClass`,
  * `ClassDecl`,
  * `VarDecl`,
  * `MethodDecl`,
  * `Type`,
  * `Statement` and
  * `Exp`.
3. Constructor names for context-free syntax rules.
4. Disambiguation rules.

For grading, it is required to comply with all sort names literally.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment2` on GitHub. Your GitHub repository contains a step-by-step procedure how to file such a request. As part of your submission,
  we ask you to provide a short explanation of the organisation of your syntax definition in `MiniJava/syntax/README.md`.
The deadline for submission is October 1, 17:59.

### Grading

You can earn up to 80 points for your concrete syntax definition.
Therefor, we run 333 test cases against your implementation. 
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* lexical syntax (20 points)
  * `ID` (6 points)
  * `INT` (1 point)
  * whitespace (3 points)
  * simple comments (5 points)
  * complex comments, including nested block comments (5 points)
* context-free syntax (30 points)
  * `Program` (1 point)
  * `MainClass` (2 points)
  * `ClassDecl` (2 points)
  * `VarDecl` (2 points)
  * `Type` (2 points)
  * `MethodDecl` (5 points)
  * `Statement` (6 points)
  * `Exp` (10 points)
* disambiguation (30 points)
  * associativity (10 points)
  * precedence (20 points)

You can earn up to 20 points for your abstract syntax definition.
Therefor, we generate an abstract syntax tree for an example program.
We inspect your syntax definition and the generated AST manually.
We particular focus on 
 meaningful constructor names, 
 distinctness of different constructs,
 and the representation of formal and actual parameter lists.

### Early Feedback

We provide early feedback for the correctness of your syntax definition.
This feedback gives you an indication which parts of your syntax definition might still be wrong.
It includes a summary on how many tests you pass and how many points you earn by passing them.

## Detailed Instructions

### Preliminaries

#### Repository Branch

We created a branch `assignment2` in the student repositories. Make sure to start your work from this branch. You are free to work on your own branches, but should submit with a pull request against `assignment2`. We will give you early feedback only on pull requests against this branch.

#### Spoofax Version

To be able to react quickly on problems you discover, we have a special update site for the course. Go to the *Help > Software Updates* menu in Eclipse. Then select "Add...", enter the site name "Spoofax IN4303", and the update site URL:

       http://download.spoofax.org/update/in4303/
       
Now select Spoofax Core (you may have to reselect the update site you just entered), and follow the steps of the wizard.

Unfortunately, you have to delete your still empty `MiniJava` project and create a new one:

1. Right-click into the Package Explorer.
2. Select **New** from the context menu.
3. Choose **Project...** from the list.
4. Select the **Spoofax editor project** wizard.
5. Switch to the **Next** tab.
6. As **Project name**, use `MiniJava`. 
7. As **Language name**, use `MiniJava`.
8. Keep the **Plugin ID and package name** as suggested by the wizard.
9. As **File extensions**, use `mjv`.
10. Select both checkboxes.
11. Press the **Finish** button.

### Agile Software Language Engineering

Spoofax supports short development cycles.
This enables you to develop your syntax definition step by step.
In each step, you focus on a single aspect, 
 for example a particular nonterminal symbol or a particular grammar rule.
After each step, you can check your progress by building the project and running your test cases. 
This requires you to declare the same start symbols in your syntax definition as in your test suites. 

You can also test the generated editor in the same Eclipse instance.
This requires you to specify the start symbol that is used by the editor
  in the main editor description `editor/MiniJava.main.esv`.
Of course, this should also be a start symbol in your syntax definition.
You can test the editor by first building your project and opening a MiniJava program.  
In Eclipse, you build a project by choosing *Build Project* from the *Project* menu or by pressing the corresponding keyboard shortcut.
When you later rebuild your project, any open MiniJava editor is updated to the new version you just built.

You can also use *Show AST* in the editor's *Transform* menu to test your abstract syntax definition interactively.
While you change a MiniJava program in the editor, its corresponding AST is updated automatically.
You might notice that the editor will give you an AST even for syntactically incorrect programs.
This is because Spoofax editors support syntactic error recovery. 

![Menu entry to inspect the abstract syntax of a program fragment.](http://strategoxt.org/pub/Spoofax/Features/show-abstract-syntax.png  "Menu entry to inspect the abstract syntax of a program fragment.")

### SDF3

You should define your syntax in [SDF3](http://metaborg.org/wiki/sdf). 
You can start by creating a new file `syntax/MiniJava.sdf3` in your MiniJava project:

    module MiniJava
    
    context-free start-symbols 

When you save this file, you should get a corresponding file `src-gen/syntax/MiniJava.sdf`.
You can also split your syntax definition over several modules in `syntax/`. 
Do only import modules that you wrote yourself.

### Lexical Syntax

Start with the lexical syntax definition including identifiers, integer, and simple layout. 
First, define lexical syntax rules:

    lexical syntax
   
      ID     = ...
      INT    = ...
      LAYOUT = ...
      

Second, define follow restrictions to ensure longest matches:

    lexical restrictions
    
      ID -/- ...
      INT -/- ...
    
    context-free restrictions
    
     LAYOUT? -/- ...
     
Finally, use rejection rules to rule out reserved words.

    lexical syntax
    
      ID = ... {reject}

In your tests, you declare `ID` and `INT` as start symbols for those tests. To get those tests running, you also need to define those as start symbols in your grammar:

    context-free start-symbols
    
      ID INT

You now can run your tests. It is important to get your grammar working at this stage. Do not move on if you have issues here, since there is a high chance that these issues influence your other tests as well. If you experience long running times for your tests, this is most likely caused by an erroneous definition of `LAYOUT`.

### Context-free Syntax

Continue with the context-free syntax of the language. 
Use the context-free grammar in the *MiniJava Language Reference Manual* as a reference.
We recommend to use templates for your context-free syntax definition,
 since this will give you a head start for the next lab.
In case you want to use `<` or `>` as symbols inside a template, you can use alternate template brackets `[...]`.

You need disambiguation constructs to disambiguate your syntax definition.
You can specify the associativity with attributes `left`, `right`, or `non-assoc`.
These attributes are added to the end of a rule:

    context-free syntax
    
      Exp.Constr1 = ... {left}

You can specify precedence in a `context-free priorities` section.
In this section, you order groups of rules:

    context-free priorities
    
      { 
        Exp.Constr1
        Exp.Constr2
      } > { ... } > ...

You can also define the associativity of a group:

    context-free priorities
    
      { left:
        Exp.Constr1
        Exp.Constr2
      } > { ... } > ...

### Comments

Finally, you should add lexical syntax rules for comments to your syntax definition. 
Start with single line comments. 
Continue with unnested block comments. 
Do not forget to define follow restrictions.

### Challenge

Challenges are meant to distinguish excellent solutions from good solutions. 
Typically, they are less guided and require more investigation or higher programming skills.
This lab's challenge is to support nested block comments.
