# Milestone 2: Semantic Analysis

In this milestone, the MiniJava editor from the previous milestone is extended with semantic analyses. The extended editor should be able

* to resolve references in a MiniJava program, 
* to calculate types of MiniJava expressions,
* to report semantic errors, warnings, and notes, and
* to provide semantic code completion.

## Prerequisites

### MiniJava Editor (Milestone 1)

We provide you with a fresh MiniJava editor project, which already covers the concrete and abstract syntax of MiniJava. For grading purposes, you are required to use this project as a starting point for milestone 2.

1. Download the MiniJava project.
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

The projects hides nearly its entire implementation from you. Only the signature for MiniJava abstract syntax trees is accessible. You can find it in  `assignment1/MiniJava.str`.
