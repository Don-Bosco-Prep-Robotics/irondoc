---
sidebar_label: 'Commands'
sidebar_position: 4
---

# Commands

A command is a schedulable description of robot behavior. In this codebase, commands are usually produced by methods on the subsystem that owns the affected hardware.

```java
public Command stop() {
  return mechanism.set(0);
}
```

This keeps hardware details inside the subsystem. `RobotContainer` and `Superstructure` only need to know which action they want.

## Commands Are Returned, Not Performed Immediately

Calling a command factory creates a command:

```java
Command stopCommand = indexer.stop();
```

It does not necessarily run the motor at that moment. The command must be scheduled by a trigger, returned as part of another command, or explicitly passed to the scheduler. Creating a command inside `periodic()` without scheduling or returning it has no effect.

## Commands in the Current Robot

- The drivetrain has a continuous default drive command.
- `zeroGyro()` returns a one-time command.
- The indexer and flywheels use `Commands.select(...)` to translate a goal into an action.
- The superstructure uses `Commands.parallel(...)` to coordinate mechanisms.
- PathPlanner creates autonomous drivetrain commands.

## Adding a Mechanism Command

1. Add the method to the subsystem that owns the mechanism.
2. Return a command with the correct subsystem requirement.
3. Decide whether it ends naturally or runs until interrupted.
4. Provide a corresponding stop or idle action.
5. Give important composed commands a useful name with `.withName(...)`.
6. Schedule it from `RobotContainer`, `Superstructure`, or an autonomous event.

For a coordinated robot action, do not duplicate motor logic in `RobotContainer`. Add or reuse subsystem commands, then combine them in `Superstructure`.

## Common Mistakes

- Constructing a command but never scheduling it.
- Omitting a subsystem requirement, allowing commands to fight over hardware.
- Using a sequence when actions need to run together.
- Creating motor controllers inside a command.
- Forgetting behavior on cancellation or button release.
- Reusing a stale command when a supplier should create a fresh one.

See [Subsystems](./subsystems) for hardware ownership and [Subsystems and Goals](../robot-code-2026/subsystems-and-goals) for the current scoring flow.
