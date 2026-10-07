---
sidebar_label: 'Robot Lifecycle'
sidebar_position: 2
---

# Robot Lifecycle

There are three main startup files: `Main.java`, `Robot.java`, and `RobotContainer.java`.

Understanding their responsibilities prevents code from being placed in a method that runs only once, or from bypassing the command scheduler.

## `Main.java`

`Main.java` is the Java entry point. WPILib starts the `Robot` class from here. This file is standard framework code and normally does not need to be edited.

## `Robot.java`

`Robot` extends AdvantageKit's `LoggedRobot`. Its constructor configures logging before creating `RobotContainer`.

On the real robot it:

1. writes WPILOG files to the roboRIO or attached USB storage;
2. publishes data through NT4 for dashboards;
3. exposes the command scheduler on SmartDashboard;
4. starts the logger; and
5. constructs every subsystem through `RobotContainer`.

`robotPeriodic()` runs the `CommandScheduler` every loop. This is what polls controller triggers, starts commands, interrupts conflicting commands, and calls subsystem periodic methods.

During autonomous, `Robot` requests a command from `RobotContainer` and schedules it. When teleop begins, that autonomous command is canceled so it cannot keep controlling the robot.

```java
public void robotPeriodic() {
  CommandScheduler.getInstance().run();
}
```

## `RobotContainer.java`

`RobotContainer` is where the robot is assembled in software. It creates:

- the Xbox controller on USB port 0;
- the swerve drivetrain;
- the intake and indexer;
- the left and right shooter flywheels; and
- the superstructure that coordinates the game-piece mechanisms.

It also assigns the drivetrain's default command. A default command runs whenever no other command requiring that subsystem is active, so teleop driving automatically resumes after another drivetrain command ends.

## Current Driver Bindings

| Input | Action |
| --- | --- |
| Left stick Y | Forward/backward translation |
| Left stick X | Sideways translation |
| Right stick X | Rotation |
| A pressed | Zero the swerve gyro |
| B pressed | Set the superstructure to `SCORING` |
| B released | Set the superstructure to `IDLE` |

The joystick values are negated in `SwerveDriveSubsystem` so the physical stick directions match the expected field movement. Alliance-relative control flips the driver perspective when needed.

:::warning
The comment above the gyro binding mentions Start + Back, but the actual code uses the A button. The binding, not the comment, determines robot behavior.
:::

## Adding a Driver Binding

Add bindings in `configureBindings()`. First obtain a `Command` from the subsystem that owns the action, then choose trigger behavior based on how the action should run.

```java
driverXbox.x().onTrue(subsystem.doOnce());
driverXbox.y().whileTrue(subsystem.runWhileHeld());
```

- `onTrue` schedules once when the button changes from released to pressed.
- `onFalse` schedules once when it changes from pressed to released.
- `whileTrue` keeps the command scheduled while held and cancels it on release.

For a maintained state such as scoring, ensure release or interruption produces a safe idle state. Give important commands names so they are recognizable in AdvantageScope and the command scheduler dashboard.

## Adding a New Subsystem

1. Create a class extending `SubsystemBase` under `subsystems/`.
2. Put its hardware IDs and control values in a matching constants group.
3. Construct its hardware once, inside that subsystem.
4. Expose actions as commands rather than public motor-controller fields.
5. Update telemetry in `periodic()` and simulation in `simulationPeriodic()` when supported.
6. Construct one instance in `RobotContainer`.
7. Bind it directly for a standalone action, or pass it to `Superstructure` for a coordinated action.

Subsystem constructors run during robot startup. Do not repeatedly construct motor controllers inside commands or periodic methods.
