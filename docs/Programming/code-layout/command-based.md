---
sidebar_label: 'Command Based Programming'
sidebar_position: 1
---

# Command-Based Programming

WPILib command-based programming separates **what the robot owns** from **what the robot is doing**.

- A **subsystem** owns hardware, such as a drivetrain or indexer.
- A **command** describes an action, such as driving, indexing, or stopping.
- A **trigger** schedules commands from a controller button or another condition.
- The **command scheduler** runs commands and prevents two commands from controlling the same subsystem at the same time.

The full WPILib explanation is available in the [command-based documentation](https://docs.wpilib.org/en/stable/docs/software/commandbased/index.html). This page explains the parts most visible in our robot code.

## The Scheduler Loop

`Robot.robotPeriodic()` calls the scheduler once every robot loop:

```java
CommandScheduler.getInstance().run();
```

During that call, the scheduler reads triggers, schedules commands, interrupts conflicts, executes active commands, runs subsystem periodic methods, and starts default commands for free subsystems.

You normally should not call a command's methods yourself. Return or schedule the command and let the scheduler manage its lifecycle.

## Requirements

A command's requirements say which subsystems it controls. Only one scheduled command can require a subsystem at a time. When a new command takes the drivetrain requirement, the default teleop command is interrupted. When the new command ends, teleop driving resumes.

Commands created with subsystem helpers such as `runOnce(...)` automatically include that subsystem. For commands created with `Commands.runOnce(...)`, pass the subsystem requirement explicitly when the command controls its hardware.

## Command Composition

Small commands can be combined:

- `andThen(...)` runs the next command after the first finishes.
- `alongWith(...)` and `Commands.parallel(...)` start commands together.
- `Commands.sequence(...)` runs commands one at a time in order.
- `Commands.select(...)` chooses a command using a value or state.

Our superstructure uses parallel groups because the shooter and indexer need to respond together during scoring.

## Choosing a Trigger Type

```java
controller.a().onTrue(subsystem.doOnce());
controller.b().whileTrue(subsystem.runContinuously());
controller.x().onFalse(subsystem.stop());
```

- Use `onTrue` for a one-time action or state change.
- Use `whileTrue` when releasing the control should cancel the action.
- Use `onFalse` when release must schedule a separate command, such as returning to idle.

## Safe Command Design

When adding a command, decide which subsystem owns the hardware, what requirement prevents conflicts, how long the command runs, what happens when it ends, and how its state can be observed.

Continue with [Commands](./command-structure), [Subsystems](./subsystems), and the [2026 Robot Lifecycle](../robot-code-2026/robot-lifecycle) to see these ideas in the team code.
