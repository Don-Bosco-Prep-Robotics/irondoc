---
sidebar_label: 'Project Overview'
sidebar_position: 1
---

# 2026 Robot Code Overview

This section teaches the programming in `4653-Rebuilt-offseason2026`. It explains what the robot currently does, how a request reaches the hardware, and where to work when adding a feature. The project is a Java 17, command-based WPILib project built with GradleRIO 2026.2.1.

## How to Use This Section

If you are new to the team, read these pages in sidebar order:

1. **Project Overview** explains the folders and the complete data flow.
2. **Robot Lifecycle** explains startup, the scheduler, controls, and autonomous handoff.
3. **Subsystems and Goals** explains how mechanisms and coordinated actions are written.
4. **Swerve Drive** explains driver input and drivetrain configuration.
5. **Autonomous** explains how PathPlanner connects to the drivetrain.
6. **Logging, Simulation, and Tests** explains how to prove that a change works.

Each page separates current behavior from future work. Code shown under a current-behavior heading describes the repository as it exists now. Admonitions marked **Current limitation** identify code that is incomplete or unsafe to extend without another change.

The project combines several libraries:

- **WPILib commands** schedule robot actions and manage subsystem requirements.
- **YAMS** wraps mechanisms such as the flywheels, indexer, and swerve drive.
- **YAGSL JSON files** describe the four swerve modules and their hardware.
- **PathPlanner** stores paths and autonomous routines in the deploy folder.
- **AdvantageKit** records robot data for live viewing and log replay.

## File Layout

```text
src/
├── main/
│   ├── java/frc/robot/
│   │   ├── Main.java
│   │   ├── Robot.java
│   │   ├── RobotContainer.java
│   │   ├── Constants.java
│   │   └── subsystems/
│   │       ├── drive/
│   │       ├── indexer/
│   │       ├── intake/
│   │       ├── shooter/
│   │       └── superstructure/
│   └── deploy/
│       ├── pathplanner/
│       └── swerve/
└── test/java/frc/robot/
    └── RobotConfigurationTest.java
```

Java code belongs in `src/main/java`. Configuration that must be copied to the roboRIO belongs in `src/main/deploy`. Tests that can run without robot hardware belong in `src/test/java`.

## The Main Data Flow

The code can be read as one chain:

```text
Driver or autonomous routine
        ↓
RobotContainer button binding / PathPlanner command
        ↓
Superstructure goal or drivetrain command
        ↓
Individual subsystem command
        ↓
YAMS mechanism and motor-controller wrapper
        ↓
Spark MAX, encoder, or simulated mechanism
```

The important idea is that high-level code asks for an outcome such as `SCORING`. Lower layers decide which motors must run to produce that outcome.

## Where Should a Change Go?

| If you want to change... | Start here |
| --- | --- |
| A controller button or joystick axis | `RobotContainer.java` |
| What happens during a robot-wide action | `Superstructure.java` |
| One mechanism's behavior | Its subsystem under `subsystems/` |
| A motor ID, limit, PID, or feedforward value | `Constants.java` or the relevant deploy JSON |
| Swerve module hardware | `src/main/deploy/swerve/base` |
| An autonomous route | `src/main/deploy/pathplanner` and `RobotContainer.java` |
| Logging destinations or replay behavior | `Robot.java` |
| Automated safety checks | `src/test/java` |

Keep decisions at the highest sensible layer. A controller button should request a goal; it should not directly set three motor outputs. A subsystem should own its hardware; unrelated classes should not reach into its motor controller.

## Recommended Change Workflow

1. Find the current behavior in these docs and then in the source code.
2. Decide which layer owns the change.
3. Make the smallest complete change, including its stop or idle behavior.
4. Add telemetry when the new state would otherwise be difficult to observe.
5. Compile and run unit tests with `.\gradlew.bat build`.
6. Test in simulation when the mechanism supports it.
7. Test on a disabled or safely supported robot before operating at full speed.
8. Update these docs when behavior, controls, hardware IDs, or configuration changes.

## Current Implementation Status

| Area | Current state |
| --- | --- |
| Swerve teleop | Implemented with a default drive command |
| Gyro reset | Bound to the driver's A button |
| Shooter | Two flywheels run together through YAMS |
| Indexer | Implemented through a YAMS flywheel mechanism |
| Superstructure scoring | Runs the shooter and indexer goals in parallel |
| Intake | Constants and intended states exist, but hardware construction is commented out |
| Autonomous | Paths exist, but PathPlanner setup is not currently called |
| Logging | AdvantageKit and high-verbosity YAMS telemetry are enabled |
| Tests | Validate deploy JSON, module references, CAN IDs, and auto path references |

:::note
This section documents what the repository currently does. Items marked incomplete should not be treated as finished robot behavior.
:::
