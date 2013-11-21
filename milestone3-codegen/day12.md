# Day 12

In this lab, you complete your code generator to handle arbitrary MiniJava programs.

## Tasks

### Write Even More Jasmin Code

Before you complete your code generator, you should come up with small example MiniJava programs, which cover the following constructs:

* fields, 
* parameters and local variables,
* variable and field access, 
* assignments, and
* method calls with arguments.

This lab requires several strategies and constructors from the libraries of milestone 2, which unfortunately are not part of your current project. You can declare missing strategies as external as you did with `index-type-of`. Declare missing constructors on your own.

Write corresponding Jasmin programs, which you expect to be the result of a MiniJava-to-Jasmin compiler. Generate Java class files from them and run them. Improve your programs until they run without errors.

## Generate Code for Fields and Field Access

You now need to extend `to-jbc` to cover field and field access. To create field descriptors, you need to provide a new strategy `to-jbc-desc`.

1. Provide rules for `to-jbc`, which translate MiniJava types into Jasmin types as used in field and method descriptors. 

2. Provide a rule for `to-jbc-desc`, which translates a field name to a field descriptor. You can retrieve required type information from the index by using  `index-type-of`.

3. Provide a rule for `to-jbc`, which translates fields from MiniJava into Jasmin fields.

4. Provide a rule for `to-jbc`, which translates field access expressions from MiniJava into sequences of Java bytecode instructions. To distinguish field from variable access, you can rely on `index-uri-namespace`, which rewrites a name to its namespace.

5. Provide a rule for `to-jbc`, which translates assignments to fields from MiniJava into sequences of Java bytecode instructions. This rule should call `to-jbc` recursively to translate expressions to Java bytecode sequences.
 
You can test each rule by selecting a code fragment in the MiniJava editor and running your code generation builder.

### Generate Code for Local Variables and Variable Access

Finally, you need to extend `to-jbc` to cover parameters, local variables,  and access to them.

1. Provide a rule for `to-jbc-desc`, which translates a method name to a method descriptor. As for fields, you can retrieve required type information from the index.

2. Extend your rule for `to-jbc`, which handles method declarations. Support parameters by generating variable declarations, which map variable numbers in generated Java bytecode to variable names in the original MiniJava program. Do the same for local variables. During this generation, it is important to remember the number associated with a variable name for later reference. You can achieve this by storing this number in the index:

    1. Define a constructor `VarNumber` of type `DefDataKind`.
    2. For each variable, retrieve its URI by applying `index-uri` to its name.
    3. For each variable, construct a term `DefData(uri, VarNumber(), num)`, where `uri` is the URI of a variable and `num` is the variable number you like to associate with this variable.
    4. Store these terms in the index by applying `index-add(|<index-get-current-file>)` to them.  
    
  
3. Extend your rule for `to-jbc`, which handles method calls. Support calls with arguments by calling `to-jbc` recursively to translate argument expressions.

4. Provide a rule for `to-jbc`, which translates variable access expressions from MiniJava into sequences of Java bytecode instructions. To distinguish variable from field access, you can rely on `index-uri-namespace`, which rewrites a name to its namespace. To get the variable number associated with a variable, apply `index-get-data(|VarNumber())` to its name.

5. Provide a rule for `to-jbc`, which translates assignments to variables from MiniJava into sequences of Java bytecode instructions. This rule should call `to-jbc` recursively to translate expressions to Java bytecode sequences.

## Challenges

Challenges are meant to distinguish excellent solutions from good solutions. Typically, they are less guided and require more investigation and programming skills.

### Generate Precise Ranges for Local Variables

A precise range of a local variable covers only the parts in the code where the variable is defined and used:

* There is only one continuous range for each variable (in contrast, variable liveness as discussed in the lecture can be fragmented). 
* The range should cover at least all instructions between the first and last load or store (whatever comes first/last) of a local variable. 
* Jumps might extend the range, since they might require a variable to survive.

You should extend your code generator to generate precise ranges.

1. Extend `to-jbc` to label load and store instructions.

2. Come up with a strategy `to-range` which maps a variable number to its range, represented as a pair of start and end labels. Similar to the stack limit challenge from last week, the analysis might be easier on the MiniJava code.

3. Integrate `to-range` into your `to-jbc` rule for methods.

### Generate Debug Information

Real-life compilers add debug information to generated code. This information is used by debuggers, but also in runtime error messages. 

Write a MiniJava program that causes a runtime exception, e.g. by accessing an array out of bound. When you compile this program to Java classes and run it in the virtual machine, you expect the runtime error to report you the position of the expression causing the error in the source code. To achieve this behaviour, you need to add source file and line number directives.

1. Extend your `to-jbc` rules for classes, to include a source file directive pointing to the MiniJava file you compile. 

2. When compiling expressions and statements, you can add line number directives in front of the resulting Java Bytecode instructions. To find out the line number of an AST element, you can apply the strategy `origin-line` from the `editor-common.generated` library. Instead of adding code for line numbers to every rule of `to-jbc` dealing with expressions and statements, you should define a new strategy `to-jbc-ln` which generates just  the line number directive and calls your original code generation strategy `to-jbc`. Next, replace applications of `to-jbc` by `to-jbc-ln` wherever you need to generate line numbers.

3. It is pretty common, that several expression occur in the same line. In this case, you should generate only a single line number directive. To achieve this, you can try to store references to line numbers in the index. When `to-jbc-ln` encounters a new line number, it should add the line number directive and store a reference to the number. In all other cases, it will find the line number already in the index and should not add another directive. 
