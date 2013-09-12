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

We highly recommend to work with a fresh Eclipse and Spoofax installation.
Follow the Spoofax [download instructions](http://metaborg.org/wiki/spoofax/download).
You may also install some additional Eclipse plug-ins to which you are used to, e.g. for version management.

### Initial Spoofax Project

You should start with a fresh Eclipse workspace and create a new, empty Spoofax project. 
Since we use automated grading tools, it is important to get the following steps right:

1. Start Eclipse.
2. Select a fresh workspace.
3. Create a new Spoofax project in your workspace:
    1. Right-click into the Package Explorer.
    2. Select **New** from the context menu.
    3. Choose **Project...** from the list.
    4. Select the **Spoofax editor project** wizard.
    5. Switch to the **Next** tab.
    6. As **Project name**, use `MiniJava-<YourNetID>`. 
       Your NetID is your login name on Blackboard. 
       It is not your student number.
    7. As **Language name**, use `MiniJava`.
    8. Keep the **Plugin ID and package name** as suggested by the wizard.
    9. As **File extensions**, use `mjv`.
    10. Select both checkboxes.
    11. Press the **Finish** button.
