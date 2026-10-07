---
sidebar_label: 'Autonomous'
sidebar_position: 5
---

# Autonomous

PathPlanner configuration lives under `src/main/deploy/pathplanner`:

- `settings.json` describes the robot's size, mass, module locations, gearing, and motion limits;
- `paths/*.path` contains field waypoints and constraints;
- `autos/*.auto` combines paths and commands into autonomous routines;
- `navgrid.json` describes field obstacles for pathfinding.

`RobotContainer.getAutonomousCommand()` currently returns:

```java
return new PathPlannerAuto("Path1");
```

The name must match `autos/Path1.auto`, not merely `paths/Path1.path`.

## AutoBuilder Setup

`SwerveDriveSubsystem.setupPathPlanner()` provides PathPlanner with:

- the estimated robot pose;
- a way to reset odometry;
- measured robot-relative chassis speeds;
- a way to command robot-relative chassis speeds;
- translation and rotation PID controllers;
- the robot model from `settings.json`; and
- an alliance check that mirrors a path for the red side.

These functions let PathPlanner calculate a desired motion while the drivetrain remains responsible for the hardware.

:::danger Required fix
`setupPathPlanner()` exists but is never called by the drivetrain constructor. A `PathPlannerAuto` requires `AutoBuilder` to be configured first. Call the setup after `drive` is created and handle its configuration exceptions before relying on autonomous mode.
:::

## Adding an Autonomous Routine

1. Open the project in PathPlanner.
2. Confirm the dimensions and drivetrain values in `settings.json`.
3. Create or edit a path.
4. Create an auto that references that path.
5. Save both files into the deploy directory.
6. Select the auto by its `.auto` name in `RobotContainer`.
7. Run `./gradlew test` to catch missing path references.
8. Test in simulation, then test on the real robot at reduced speed with the robot ready to be disabled.

The current project includes `Path1` and `New Auto`. Only `Path1` is selected by the Java code.
