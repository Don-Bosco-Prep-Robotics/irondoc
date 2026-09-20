---
sidebar_label: 'Commands'
sidebar_position: 4
---

# Commands

We do Command structure in two ways, depending on the subsystem being called. The Drive Command is usually just one large command, which, especially in the case of swerve, does a good deal of math and other stuff inside it. Commands for things like a climber, where there are many commands doing small things, are usually all put in one Class, which holds multiple Command instances