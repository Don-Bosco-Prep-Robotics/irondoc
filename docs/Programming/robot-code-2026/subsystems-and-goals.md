---
sidebar_label: 'Subsystems and Goals'
sidebar_position: 3
---

# Subsystems and Goals

The mechanism code uses three layers:

1. **Constants** describe hardware IDs, gearing, limits, PID, feedforward, and telemetry.
2. **Mechanism subsystems** wrap Spark MAX controllers in YAMS objects and expose commands.
3. **The superstructure** coordinates several mechanisms using named robot goals.

This separation makes changes easier to locate. Motor configuration belongs in constants, one mechanism's behavior belongs in its subsystem, and actions involving several mechanisms belong in the superstructure.

## Shooter

Each `Flywheel` object owns one Spark MAX, one YAMS `SparkWrapper`, and one YAMS `FlyWheel`. The left motor uses CAN ID 14 and the right motor uses CAN ID 13. Both use a 4-inch wheel model, a 1.17:1 reduction, closed-loop control, feedforward, and a 40-amp stator limit.

`Flywheels` combines the two sides. Its `SHOOTING` goal runs both at 58.63 rotations per second, which is about 3518 RPM. Its `IDLE` goal commands zero output.

## Indexer

The indexer uses Spark MAX CAN ID 20. It is modeled as a YAMS flywheel because the important controlled value is rotational speed, even though it is moving a game piece rather than acting as a shooter wheel.

Its goals are:

- `INDEXING`: run at the configured 30 RPM;
- `IDLE`: command zero output.

Both the shooter and indexer call `updateTelemetry()` in `periodic()` and `simIterate()` during simulation.

## Superstructure

The `Superstructure` gives the rest of the robot one place to request a complete action. It maps a high-level goal to the commands needed by its child mechanisms.

```java
Goal.SCORING -> flywheels SHOOTING + indexer INDEXING
Goal.IDLE    -> flywheels IDLE     + indexer IDLE
```

The scoring and idle child commands run in parallel. This matters because the flywheels and indexer must respond together; a sequence would wait for one command to finish before starting the next.

The current superstructure goal and current command are logged, which makes it easier to answer two different debugging questions:

- What state did the robot request?
- What command is actually scheduled now?

## Intake

The intended intake has two mechanisms:

- a pivoting frame on CAN ID 21 with 0-to-90-degree software limits;
- a roller on CAN ID 22.

The constants and goal names exist, but the constructor and telemetry code are commented out. `INTAKING` is currently an empty superstructure command, and `OUTTAKING` has no command in the superstructure map.

:::danger Current limitation
Calling `setGoal(OUTTAKING)` will attempt to read a missing map entry and can cause a null-pointer error. Do not bind that goal until it has an implementation.
:::

## Adding a New Goal Safely

When adding a superstructure goal:

1. add the enum value;
2. add a command supplier to `goalCommands`;
3. make every participating subsystem require the correct hardware;
4. define what happens when the action stops;
5. add a button binding or autonomous event; and
6. verify the logged goal and current command in AdvantageScope.

Using a `Supplier<Command>` creates a fresh command when the goal is requested instead of reusing an old command instance.

## Modifying an Existing Mechanism

1. Confirm the physical hardware and CAN ID with the electrical team.
2. Update the constants or configuration object.
3. Change the subsystem command that owns the behavior.
4. Update the superstructure mapping if the robot-wide action changes.
5. Update a controller binding only if the driver interface changes.
6. Add telemetry for new setpoints, measurements, or goal states.
7. Build, simulate when possible, and test at a safe reduced output.

Do not tune PID or feedforward by changing several layers at once. Keep a record of the old values, change one part of the controller, and use logged setpoint-versus-measurement data to judge the result.
