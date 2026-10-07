---
sidebar_label: 'File Structure'
sidebar_position: 2
---


# Code Structure

The current robot project separates Java source, deploy-time configuration, tests, and build configuration:

```text
src/
├── main/
│   ├── java/frc/robot/       Java robot code
│   └── deploy/               Files copied to the roboRIO
└── test/java/frc/robot/      Desktop unit tests
vendordeps/                   Third-party library definitions
build.gradle                  Build, simulation, test, and deploy setup
```

## Main Java Files

- `Main.java` starts the WPILib robot program. It is framework code and rarely changes.
- `Robot.java` configures logging, runs the command scheduler, and manages autonomous/teleop transitions.
- `RobotContainer.java` constructs subsystems, defines controller bindings, assigns default commands, and selects autonomous.
- `Constants.java` stores mechanism configuration, CAN IDs, gains, gearing, limits, and setpoints.

The 2026 project does not currently have a `RobotState.java`. Pose is owned by the drivetrain, while mechanism goals are owned by their subsystems and the superstructure.

## Subsystem Packages

Subsystems are grouped by mechanism under `frc/robot/subsystems`:

- `drive` owns swerve behavior;
- `shooter` owns the two flywheels;
- `indexer` moves game pieces into the shooter;
- `intake` contains the developing intake mechanism; and
- `superstructure` coordinates mechanisms into robot-wide goals.

The current project generally creates command factories inside these subsystem classes instead of keeping a separate `commands` directory.

## Deploy Configuration

`src/main/deploy` is part of the program even though it is not Java. Gradle copies it to `/home/lvuser/deploy` on the roboRIO.

- `swerve/` contains YAGSL module hardware and tuning.
- `pathplanner/` contains the robot model, paths, autos, and navigation grid.

If an autonomous file or swerve JSON file is missing from this directory, the Java classes that load it cannot reconstruct it automatically.

## Tests

Desktop tests belong under `src/test/java`. Keep hardware-free checks here, such as validating JSON, CAN IDs, and autonomous references. Tests should not construct real motor controllers or require the roboRIO HAL unless the test environment is deliberately configured for it.

See the [2026 Project Overview](../robot-code-2026/overview) for the complete flow through these files.
