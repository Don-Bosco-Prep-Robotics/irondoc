---
sidebar_label: 'Subsystems'
sidebar_position: 3
---

# Subsystems

A subsystem represents a part of the robot that operates as a unit, such as the drivetrain, indexer, intake, or shooter. See the [WPILib subsystem documentation](https://docs.wpilib.org/en/stable/docs/software/commandbased/subsystems.html) for the framework details.

## What a Subsystem Owns

A subsystem should own its motor controllers and sensors, mechanism wrappers, commands, measurements, and periodic telemetry updates. Construct each hardware object once. Other classes should ask the subsystem to perform an action instead of accessing its motor controller directly.

## `periodic()` and `simulationPeriodic()`

The scheduler calls `periodic()` every robot loop. Use it for telemetry, sensor-derived state, and lightweight updates that must always occur. Do not create unscheduled commands there and expect them to run.

WPILib calls `simulationPeriodic()` during simulation. The current YAMS mechanisms use this method to advance their simulated physics.

## Default Commands

A default command runs whenever the subsystem is not required by another command. The swerve subsystem uses teleop driving as its default. An autonomous or alignment command can temporarily take control, and normal driving resumes when it releases the drivetrain.

## Adding a Subsystem

1. Create a class extending `SubsystemBase` in a clearly named package.
2. Add a constants group for hardware IDs and configuration.
3. Construct hardware and wrappers in the subsystem constructor.
4. Expose commands and read-only measurements through methods.
5. Add telemetry and simulation updates.
6. Construct one instance in `RobotContainer`.
7. Add a default command, button binding, or superstructure integration.
8. Add tests for configuration that can be checked without hardware.

The [2026 Subsystems and Goals](../robot-code-2026/subsystems-and-goals) page walks through the exact layers used by the current shooter, indexer, intake, and superstructure.
