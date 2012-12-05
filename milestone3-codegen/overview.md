# Milestone 3: Code Generation

In this milestone, you extend the MiniJava editor from the previous milestone to a MiniJava compiler, which is able to generate Java class files from MiniJava programs.

## Prerequisites

### Jasmin Editor

[Jasmin][] is an assembler for the Java Virtual Machine. It takes ASCII descriptions of Java classes, written in a simple assembler-like syntax using the Java Virtual Machine instruction set. It converts them into binary Java class files, suitable for loading by a Java runtime system.
We provide you with a Jasmin editor which was built with Spoofax.

#### Installation

1. Download the Jasmin editor project from [GitHub][GitHub-Jasmin]. You can either clone the project, or download the zip file.
2. Import the project into your workspace:
    1. right-click into the Package Explorer
    2. select **Import...** from the context menu
    3. choose **General/Existing Projects into Workspace** from the list
    4. select **archive file** (2nd choice) as a source
    5. select the downloaded Jasmin project
    6. press the **Finish** button
3. Build the project:
    1. select the project folder
    2. select **Build Project** from the **Project** menu
    3. the console will report success or failure

#### Usage

You can edit Jasmin files with a Spoofax editor. The editor's *Transform* menu allows you to inspect the abstract syntax of Jasmin files and to generate Java class files from them. The project comes with a number of example programs. You should explore them in order to get used to Jasmin's  syntax.

[Jasmin]: http://jasmin.sourceforge.net/ (Jasmin Home Page)
[GitHub-Jasmin]: https://github.com/metaborg/spoofax-jasmin (Jasmin Editor Project on GitHub)

### MiniJava Editor (Milestone 2)

We provide you with a fresh MiniJava editor project, which already covers concrete and abstract syntax, name analysis, type analysis, and constraint checking for MiniJava. For grading purposes, you are required to use this project as a starting point for milestone 3.

#### Installation

1. Download the MiniJava project from [GitHub][GitHub-CC].
2. Import the project into your workspace:
    1. right-click into the Package Explorer
    2. select **Import...** from the context menu
    3. choose **General/Existing Projects into Workspace** from the list
    4. select **archive file** (2nd choice) as a source
    5. select the downloaded MiniJava project
    6. press the **Finish** button
3. Build the project:
    1. select the project folder
    2. select **Build Project** from the **Project** menu
    3. the console will report success or failure

[GitHub-CC]: https://github.com/guwac/compiler-construction/downloads (Initial MiniJava Project on GitHub)

#### Implementation Details

The projects hides nearly its entire implementation from you. Only the signature for MiniJava abstract syntax trees is accessible. You can find it in  `lib/assignments/MiniJava.str`.

## Material

We consider the following material to be useful for this milestone.

### Lecture on Code Generation

In this lecture, we discussed the architecture of the JVM, some Java bytecode instructions, and different approaches to code generation in Spoofax.

<iframe src="http://www.slideshare.net/slideshow/embed_code/5257918?rel=0" width="597" height="486" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC;border-width:1px 1px 0;margin-bottom:5px" allowfullscreen webkitallowfullscreen mozallowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="http://www.slideshare.net/guwac/declarative-semantics-definition-code-generation" title="Declarative Semantics Definition - Code Generation " target="_blank">Declarative Semantics Definition - Code Generation </a> </strong> from <strong><a href="http://www.slideshare.net/guwac" target="_blank">Guido Wachsmuth</a></strong> </div>

### Java Virtual Machine Specification

Oracle provides an [online version][JVM] of the Java Virtual Machine Specification. Chapters 3, 4, and 6 are particular helpful for this milestone. Furthermore, you can find a comprehensive list of [Java bytecode instructions][] in the English Wikipedia.

[JVM]: http://java.sun.com/docs/books/jvms/second_edition/html/VMSpecTOC.doc.html (The Java Virtual Machine Specification, 2nd edition)
[Java bytecode instructions]: http://en.wikipedia.org/wiki/Java_bytecode_instruction_listings (List of Java bytecode instructions)

### Stratego Standard Library

Many useful strategies from Stratego's standard library are documented in this [API Documentation][LibDoc].

[LibDoc]: http://releases.strategoxt.org/docs/api/libstratego-lib/stable/docs/ (Stratego Library API Documentation)