# Milestone 3: Code Generation

In this milestone, you extend the MiniJava editor from the previous milestone to a MiniJava compiler, which is able to generate Java class files from MiniJava programs.

## Prerequisites

### Jasmin Editor

[Jasmin][] is an assembler for the Java Virtual Machine. It takes ASCII descriptions of Java classes, written in a simple assembler-like syntax using the Java Virtual Machine instruction set. It converts them into binary Java class files, suitable for loading by a Java runtime system.
We provide you with a Jasmin editor which was built with Spoofax.

#### Installation

1. Choose *Install New Software...* from the *Help* menu.
2. Use the JasminXT update site `http://download.spoofax.org/update/jasmin` in the *Work with:* text box.
3. Tick *JasminXT* and choose *Next*.
4. Choose *Next*, accept the license agreement, and choose *Finish*.
5. Wait for JasminXT to be installed and restart Eclipse.

#### Usage

You can edit Jasmin files with a Spoofax editor. The editor's menu entries allow you to inspect the abstract syntax of Jasmin files, to generate Java class files from them, and to execute those class files. The project comes with a number of example programs. You should explore them in order to get used to Jasmin's syntax.

[GitHub-Jasmin]: https://github.com/metaborg/spoofax-jasmin (Jasmin Editor Project on GitHub)
[Jasmin]: http://jasmin.sourceforge.net/ (Jasmin Home Page)

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
