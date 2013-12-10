# Day 12: Complete Code Generation

In this lab, you complete your code generator to handle arbitrary MiniJava programs.

## Detailed Instructions

### Write Even More Jasmin Code

Before you complete your code generator, you should come up with small example MiniJava programs, which cover the following constructs:

* fields, 
* parameters and local variables,
* variable and field access, 
* assignments, and
* method calls with arguments.

Write corresponding Jasmin programs, which you expect to be the result of a MiniJava-to-Jasmin compiler. 
Generate Java class files from them and run them. 
Improve your programs until they run without errors.

### Generate Code for Fields and Field Access

You now need to extend your code generator to handle field declarations and field access. 

1. Provide rules for `type-to-jbc`, which translate MiniJava types into Jasmin types as used in field and method descriptors. 

2. Provide a rule for `field-to-jbc`, which translates field declarations from MiniJava into Jasmin field declarations.

3. Extend your rules for `class-to-jbc` to handle field declarations.

4. Provide a rule for `exp-to-jbc`, which translates field access expressions from MiniJava into sequences of Java bytecode instructions.
 
5. Provide a rule for `stmt-to-jbc`, which translates assignments to fields from MiniJava into sequences of Java bytecode instructions. 
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

6. Provide a rule for `stmt-to-jbc`, which translates array assignments to fields from MiniJava into sequences of Java bytecode instructions.

You can test each rule by selecting a code fragment in the MiniJava editor and running your code generation builder.

### Generate Code for Local Variables and Variable Access

Finally, you need to extend your code generator to cover parameters, local variables,  and access to them.

1. Declare a custom property `var-index` of type `Int` for the namespace `Variable` in an NaBL file.
   See the challenges from last week on how to declare such a property.

2. Store the index `i` of each parameter and local variable on its name by applying `store-var-index(|ctx, i)` to its name.
   See the challenges from last week on how to store properties on names.
   In order to calculate indices, you need to match the method declaration, not the parameter or variable declarations.
   The following strategies might be useful:
    * `map-with-index(s)` works like `map(s)`, but applies `s` to pairs `(i, e)` where `e` is an element of the list and `i` is the index of this element in the list.   
    * `nmap(s|i)` maps `s` over a list where the strategy `s` takes the index `i` as a term argument and increments `i` after each element.   

   Parameters are indexed from left to right, starting with `1`.
   Local variables can be indexed in arbitrary order, starting with the next available index.
   
3. Extend your rule for `method-to-jbc`, which handles method declarations.
   Support parameters by generating variable declarations, which map variable numbers in generated Java bytecode to variable names in the original MiniJava program. 
   Do the same for local variables. 
   To get the variable number associated with a parameter or local variable, apply `get-var-index` to its name.
  
3. Extend your rule for `exp-to-jbc`, which handles method calls.
   Support calls with arguments by calling `exp-to-jbc` recursively to translate argument expressions.

4. Provide a rule for `exp-to-jbc`, which translates variable access expressions from MiniJava into sequences of Java bytecode instructions.

5. Provide a rule for `stmt-to-jbc`, which translates assignments to variables from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

6. Provide a rule for `stmt-to-jbc`, which translates array assignments to variables from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

### Challenges

Challenges are meant to distinguish excellent solutions from good solutions. Typically, they are less guided and require more investigation and programming skills.

#### Generate Debug Information

Real-life compilers add debug information to generated code. 
This information is used by debuggers, but also in runtime error messages. 

Write a MiniJava program that causes a runtime exception, e.g. by accessing an array out of bound. 
When you compile this program to Java classes and run it in the virtual machine, you expect the runtime error to report you the position of the expression causing the error in the source code. 
To achieve this behaviour, you need to add source file and line number directives.

1. Extend your `class-to-jbc` rules for classes, to include a source file directive pointing to the MiniJava file you compile. 

2. When compiling expressions and statements, you can add line number directives in front of the resulting Java Bytecode instructions. 
   To find out the line number of an AST element, you can apply the strategy `origin-line` from the `editor/origins` library. 
   Instead of adding code for line numbers to every rule of `exp-to-jbc` and `stmt-to-jbc`, you should define new strategies `exp-to-jbc-ln` and `stmt-to-jbc-ln` which generates just  the line number directive and calls your original code generation strategies `exp-to-jbc` or `stmt-to-jbc`, respectively. 
Next, replace applications of `exp-to-jbc` and `stmt-to-jbc` by `exp-to-jbc-ln` and `stmt-to-jbc-ln` wherever you need to generate line numbers.

3. It is pretty common, that several expression occur in the same line. 
   In this case, you should generate only a single line number directive. 
   To achieve this, you can try to store references to line numbers in the index. 
   When you encounter a new line number, you should add the line number directive and store a reference to the number.    In all other cases, you should find the line number already in the index and should not add another directive. 

#### Generate Precise Ranges for Local Variables

A precise range of a local variable covers only the parts in the code where the variable is defined and used:

* There is only one continuous range for each variable (in contrast, variable liveness as discussed in the lecture can be fragmented). 
* The range should cover at least all instructions between the first and last load or store (whatever comes first/last) of a local variable. 
* Jumps might extend the range, since they might require a variable to survive.

You should extend your code generator to generate precise ranges.

1. Extend `stmt-to-jbc` to label the begin and end of instructions for a statement.

2. Come up with a strategy `to-range` which maps a variable number to its range, represented as a pair of start and end labels. 
   Similar to the stack limit challenge from last week, the analysis might be easier on the MiniJava code.

3. Integrate `to-range` into your `method-to-jbc` rule.

