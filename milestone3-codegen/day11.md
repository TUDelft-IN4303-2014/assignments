# Day 11: More Code Generation

In this lab, you extend your code generator to handle expressions, statements, methods without local parameters, and classes without fields.

## Detailed Instructions

### Write More Jasmin Code

Before you extend your code generator, you should come up with small example MiniJava programs, which cover the following constructs:

* all kinds of unary and binary expressions,
* `this` expressions,
* method calls without arguments,
* block statements, 
* `if` statements,
* `while` statements,
* methods without parameters and local variables,
* classes without fields.

Write corresponding Jasmin programs, which you expect to be the result of a MiniJava-to-Jasmin compiler. 
Generate Java class files from them and run them. 
Improve your programs until they run without errors.

### Generate Code for Expressions and Statements

You now need to extend `stmt-to-jbc` and `exp-to-jbc` to cover all statements except assignments. 

1. Provide a rule for `stmt-to-jbc`, which translates a block statement from MiniJava into a sequence of Java bytecode instructions. 
   This rule should call `stmt-to-jbc` recursively to translate the inner statements to Java bytecode sequences. 

2. Provide rules for `exp-to-jbc`, which translate logic constants `true` and `false` from MiniJava into sequences of Java bytecode instructions. 

3. Provide rules for `stmt-to-jbc`, which translate `if` and `while` statements from MiniJava into sequences of Java bytecode instructions. 
   These rules should call `stmt-to-jbc` recursively to translate inner statements to Java bytecode sequences.

4. Provide rules for `exp-to-jbc`, which translate unary and binary expressions from MiniJava into sequences of Java bytecode instructions. 
   These rules should call `exp-to-jbc` recursively to translate subexpressions to Java bytecode sequences.
   Furthermore, they should call a strategy `op-to-jbc` to translate unary and binary operators to Java bytcode instructions.
   The only exception is the `&&` operator, which needs to be treated differently, due to its lazy evaluation semantics.

You can test each rule by selecting a code fragment in the MiniJava editor and running your code generation builder.

### Generate Multiple Class Files

At this point, you can generate only a single Jasmin class file from the main class of a MiniJava program. In general, you need to generate a class file for each class in a MiniJava program. To achieve this, you need to adapt the interface of your builders. Currently, you return a pair of file name and file content:

    generate-jbc: (selected, position, ast, path, project-path) ->(filename, result)

Spoofax takes this information, creates the file if needed, and (over-)writes its content. You can also return a constant `None()`:

    generate-jbc: (selected, position, ast, path, project-path) -> None()

This tells Spoofax not to create or write any files. Instead, you need to create and write any files on your own.

2. Adapt `generate-jbc` to use `write-file` from the last lab in order to generate multiple Jasmin class files.

3. Provide rules for `class-to-jbc`, which translate empty classes from MiniJava into Jasmin class files. 

4. Extend your builder for Java class files to handle multiple class files.
 
### Code Generation for Methods

Now you need to define a strategy `method-to-jbc` to handle methods without local variables.

1. Provide a rule for `method-to-jbc`, which translates a method without parameters and local variables from MiniJava into a Jasmin method. This rule should call `stmt-to-jbc` to translate the statements of the method to a Java bytecode sequence.

2. Provide a rule for `exp-to-jbc`, which translates `this` expressions from MiniJava into sequences of Java bytecode instructions. 

3. Provide a rule for `exp-to-jbc`, which translates method calls without arguments from MiniJava into sequences of Java bytecode instructions. This rule should call `exp-to-jbc` recursively to translate subexpressions to Java bytecode sequences.

4. Extend the rule for `class-to-jbc`, which handles empty classes, in order to include code generation for methods.

## Challenges

Challenges are meant to distinguish excellent solutions from good solutions. 
Typically, they are less guided and require more investigation and programming skills.

### Use Overlays

Code generation rules become hard to read, when you construct large ASTs. 
Often, these rules inject only small parts into a skeleton AST. 
For example, the only variable parts in the default constructor of a class are its name `c` and the name of its super class `p`:

    JBCMethod(
      [PUBLIC()]
    , Init()
    , JBCMethodDesc([], Void())
    , [ JBCLimitStack("1"), JBCLimitLocals("1")
      , JBCVarDecl(VarNum(0), "this", Reference(c), LabelRef("start"), LabelRef("end"))
      , JBCLabel("start")
      , ALOAD_0()
      , INVOKESPECIAL(JBCMethodRef(CRef(p), MRef(Init()), JBCMethodDesc([], Void())))
      , RETURN()
      , JBCLabel("end")
      ]
    )

Furthermore, you might use the same pattern in different rules, e.g. in code generation rules for the main class and for ordinary classes. 
One way to avoid code duplication, are helper strategies. 
For example, you can have a strategy `to-jbc-constructor` which rewrites a pair of class and super class names to the default constructor. 
Alternatively, you can use _overlays_, which allow the specification of pattern abstractions.

Overlays are specified in their own section. 
They are named, can be parametrised, and can be nested:

    overlays
     
      DEFAULT-CONSTRUCTOR(c, p) =
        JBCMethod(
          [PUBLIC()]
        , Init()
        , JBCMethodDesc([], Void())
        , [ JBCLimitStack("1"), JBCLimitLocals("1")
          , THIS-DECLARATION(c)
          , JBCLabel("start")
          , ALOAD_0()
          , INVOKE-CONSTRUCTOR(p)
          , RETURN()
          , JBCLabel("end")
          ]
        )

You can use overlays like terms in your rules, both for matching and building:

    rules
     
      class-to-jbc: 
       Class(c, e, fs, ms) -> JBCFile(header, jbc-fs, [ DEFAULT-CONSTRUCTOR(c, p) | jbc-ms ]) 
       where ...

You should identify AST patterns in your code generation rules and come up with overlays to improve the readability of these rules.

### Precise Stack Limit Directives

A stack limit directive tells the Java Virtual Machine the maximum number of elements at the operand stack. 
To give a precise limit, you need to write a strategy `stack-limit` that maps MiniJava expressions and statements to a stack limit ( **IMPORTANT**: Do not do this analysis on the Java Bytecode level). 
For expressions, you need to consider the number of elements already on the stack before the expression is evaluated. You can either pass this number as a term parameter or adapt intermediate results accordingly. 
For statements, you do not need this information since statements should not leave any elements on the stack.

The following strategies might be useful:

* `inc`  rewrites an integer number to its successor.
* `max` rewrites a pair of integer numbers to the maximum of both numbers.
* `addi` rewrites a pair of integer numbers to the sum of both numbers.
* `foldr(s1, s2, f)` right-folds a list. 
  `s1` yields the starting point of the folding, `s2` is the folding strategy, and `f` is applied to each element just before each folding step.

### Reusable Method Descriptors

In Java Bytecode, method declarations and method calls include a method descriptor.
You can construct such an descriptor from the type associated with a method name.
In the current setup, you do this construction once for a method declaration and once for each method call.
However, the descriptor for a call is the same as for the declaration the call refers to.
You can avoid the reconstruction by storing a custom property on the method name.

First, you need to declare a property `descriptor` for namespace `Method` in a NaBL file:

    properties
     
      descriptor of Method: MethodDescriptor
      
Next, you need to store the property at a method declaration by defining a rewrite rule for `nabl-prop-site`:

    nabl-prop-site(|lang, ctx, uris, states, implicits):
      Method(ty, mname, param*, var*, stmt*, exp) -> <fail>
      where
        descr := ... // create descriptor
      ; <store-descriptor(|ctx, descr)> mname

Finally, you can access the descriptor in your code generation rules with `get-descriptor`:

    exp-to-jbc:
      Call(e, mname, e*) -> ...
      where
        descr := <get-descriptor> mname
      ; ...
      
