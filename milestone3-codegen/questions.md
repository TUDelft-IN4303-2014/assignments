# Milestone 3

#### My menu entries keep disappearing

This is due to a bug in one of the dependencies of Spoofax, which sometimes throws a `NullPointerException`.
There is no known solution at the time, but for some people upgrading from Eclipse 4.2 to 4.3 decreased the problem occurrences.
As a workaround, you can restart eclipse with *File->Restart*. 
Eclipse should restore all windows etc. and your menu should be complete again.

#### My builder is applied to the unanalysed, non-desugared AST. How can it be applied to the analysed, desugared AST?

Remove the `(source)` annotation from the action entry in your `MiniJava-Menus.esv` file.
This annotation tells Spoofax to apply a builder to the unanalysed source AST.
Without this annotation, the builder will be applied to the analysed AST.
Do not call `desugar-all` or `desugar-after-all` yourself in the builder.
Though this will give you a desugared AST, this AST will still be unanalysed and miss information you need for code generation.
