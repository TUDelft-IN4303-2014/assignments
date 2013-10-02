# Day 3: Syntactic Editor Services

In this lab, you add a pretty-printer and syntactic editor services to your MiniJava editor.

## Overview

### Objectives

1. Extend generated folding patterns with hand-written patterns. 
You should include only structures, where folding is reasonable. 
2. Integrate generated pretty-printing rules and completion templates into your MiniJava editor.
Improve your syntax definition to support 
  * textual labels in these templates,
  * hiding optional placeholders,
  * multi-line templates and
  * consistent indentation.
3. Extend your editor with additional, hand-written completion templates.

### Submission

You need to submit your MiniJava project on Blackboard. 
As part of your submission,
  we ask you to provide a short paragraph explaining the organisation of your syntax definition.
The deadline for submission is October 9, 17:59.

### Grading

You can earn up to 10 points for your folding patterns.
We will take the overall editor experience into account and
we will deduct points for too many (and by this unusable) folding options.

You can further earn up to 70 points for your improved templates.
We will focus on readability of pretty-printed code and completion suggestions and 
on consistent indentations.

Finally, you can earn up to 20 points for hand-written completion templates.

### Spoofax Update

We fixed some bugs in SDF3.
When you want to benefit from these fixes, you should update Spoofax to the latest unstable release.

1. Choose *Install New Software* from the *Help* menu.
2. Choose `http://download.spoofax.org/update/unstable/` as an update site.
3. Install Spoofax. Eclipse will tell you it is already installed and suggests an update. This is fine.

## Detailed Instructions
