---
sidebar_label: 'Control Systems'
sidebar_position: 1
---

# Control Systems Info

Control Systems are among the most important parts of programming a robot, perhaps the most important.
They are how we make the robot do things without constant input.

#### Loop Types

There are two types of control loops.  

**Open Loops** do exactly what they are told, *and no more*. When a motor is run in an open loop, it takes a setpoint and gets there eventually. For example, our standard TeleOp drive code is open loop.  

**Closed Loops** work to get where they are told *as fast as possible*. If a motor is running at .3, and it is given a setpoint of .8, it will accelerate as fast as possible until it reaches .8, and then slows down right before it gets there.

Autonomous is closed loop. It is handed a setpoint by **Pathplanner**, and works to get there and stay there. This minimizes the work that [Pathplanner](../auto/pathplanner) has to do.

#### Feedback

The way that closed loops regulate themselves is through something called a **feedback loop**. [PID](./pid) is a good example of this. Feedback works by accounting for error *that already exists in the system*. 