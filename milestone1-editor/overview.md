# Milestone 1: Syntax Analysis

In the first milestone, an initial Spoofax project is extended to a basic MiniJava editor, that is able 

* to report syntax errors, 
* to assist the creation of MiniJava programs with code templates,
* to show the abstract syntax of a MiniJava program, 
* to provide an outline view of a MiniJava program,
* to support code folding, and
* to pretty-print a MiniJava program.

## Prerequisites

### Spoofax Installation

We highly recommend to work with a fresh Spoofax installation. You may also install some additional Eclipse plug-ins to which you are used to, e.g. for version management.

### Initial Spoofax Project

We provide you with an initially empty MiniJava editor project. For grading purposes, you are required to use this project as a starting point for milestone 1.

#### Installation

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
