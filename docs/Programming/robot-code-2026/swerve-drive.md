---
sidebar_label: 'Swerve Drive'
sidebar_position: 4
---

# Swerve Drive

`SwerveDriveSubsystem` connects driver input, YAMS, and the YAGSL configuration files.

## Configuration Split

The Java constructor contains behavior-level settings:

- starting pose: `(3 m, 3 m, 0°)`;
- translation PID: `P = 4`;
- rotation PID: `P = 1`;
- maximum commanded speed: `3 m/s`;
- maximum commanded rotation: `360°/s`; and
- high telemetry verbosity.

The files under `src/main/deploy/swerve/base` contain hardware-level settings. They define the Pigeon 2 gyro on CAN ID 18 and four modules:

| Module | Drive motor | Angle motor | CANCoder | Location in inches |
| --- | ---: | ---: | ---: | --- |
| Front left | 1 | 2 | 9 | `(+11.25, +11.25)` |
| Front right | 3 | 4 | 10 | `(+11.25, -11.25)` |
| Back left | 5 | 6 | 11 | `(-11.25, +11.25)` |
| Back right | 7 | 8 | 12 | `(-11.25, -11.25)` |

All four modules use NEO motors, 4-inch wheels, an 8.14:1 drive reduction, and a 12.8:1 steering reduction. Real and simulated PID values are stored in separate JSON files.

:::tip
The JSON files are deployed to the roboRIO. Changing only Java code will not correct a wrong module CAN ID, encoder offset, motor inversion, or physical location.
:::

## Teleop Drive Flow

`RobotContainer` passes controller suppliers to `getAngularVelocityStream()`. Suppliers are used so the latest joystick value is read every scheduler loop.

The stream applies the maximum speed limits and alliance-relative control. The subsystem then converts the requested field-relative movement into robot-relative `ChassisSpeeds` using the current gyro angle.

`periodic()` publishes drivetrain telemetry. `simulationPeriodic()` advances the YAMS drivetrain simulation.

## Gyro Note

The subsystem constructs a navX object, but the line that supplies it to YAMS is commented out. The active YAGSL configuration instead declares a Pigeon 2 on CAN ID 18. The unused navX field should be removed or deliberately restored so there is only one clear gyro source.

## Common Swerve Changes

| Change | Edit |
| --- | --- |
| Controller axis or button | `RobotContainer.java` |
| Maximum teleop speed | `SwerveDriveSubsystem.java` |
| Drive or steering CAN ID | module JSON file |
| Encoder offset | module JSON file |
| Wheel size or gearing | `physicalproperties.json` |
| Real/sim module PID | matching `pidfproperties` JSON file |
| Path-following PID | `setupPathPlanner()` |
