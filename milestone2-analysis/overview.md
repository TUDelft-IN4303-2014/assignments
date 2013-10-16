# Milestone 2: Semantic Analysis

In this milestone, the MiniJava editor from the previous milestone is extended with semantic analyses. The extended editor should be able

* to resolve references in a MiniJava program, 
* to calculate types of MiniJava expressions,
* to report semantic errors, warnings, and notes, and
* to provide semantic code completion.

## Prerequisites

### MiniJava Editor (Milestone 1)

In the last assignment of milestone 1, we provided you with a fresh MiniJava editor project, 
  which already covers the concrete and abstract syntax of MiniJava. 
For grading purposes, you are required to continue with this project as a starting point for milestone 2.
The projects hides nearly its entire implementation from you. 
Only the signature for MiniJava abstract syntax trees is accessible. You can find it in  `assignment1/MiniJava.str`.

### Test Project

You should create a separate project for your test cases and example programs:

1. Right-click into the Package Explorer.
2. Select **New** from the context menu.
3. Choose **Project...** from the list.
4. Select the **Project** wizard from **General**.
5. Switch to the **Next** tab.
6. As **Project name**, use `MiniJava-tests-<YourNetID>`. 
7. Press the **Finish** button.

Please do not resubmit your syntax test cases during this milestone.

## Resources

You can find the *MiniJava Language Reference Manual* in the appendix of the book 
*Modern Compiler Implementation in Java* (2nd edition).
The relevant pages are available on [Google books](http://books.google.com/books?id=JNs6fWkJZbAC&pg=PA484).

The [MiniJava project](http://www.cambridge.org/us/features/052182060X/)
by Joao Cangussu, Jens Palsberg and Vidyut Samanta provides some example programs 
and an HTML version of the reference manual.

The following research papers cover relevant topics of this milestone:

1. L. Kats, R. Vermaas, E. Visser: [Integrated Language Definition Testing: Enabling Test-Driven Language Development](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2011-011.pdf), OOPSLA 2011
2. G. Konat, L. Kats, G. Wachsmuth, E. Visser: [Declarative Name Binding and Scope Rules](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2012-015.pdf), SLE 2012


