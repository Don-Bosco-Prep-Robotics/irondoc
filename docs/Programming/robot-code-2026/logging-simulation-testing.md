---
sidebar_label: 'Logging, Simulation, and Tests'
sidebar_position: 6
---

# Logging, Simulation, and Tests

## AdvantageKit Logging

`Robot` extends `LoggedRobot` and records the project name as log metadata.

On the real robot, AdvantageKit writes a WPILOG and publishes NT4 data. Goal fields marked with `@AutoLogOutput` are automatically available for analysis, while the superstructure explicitly records its current command with `Logger.recordOutput()`.

YAMS is also configured for high telemetry verbosity on the drivetrain, shooter motors, flywheels, and indexer. This provides the measurements needed to compare requested behavior with real behavior.

## Replay and Simulation

The mechanism subsystems implement `simulationPeriodic()` and call YAMS simulation methods. Gradle also enables the WPILib simulation GUI and Driver Station.

The current `Robot` class treats every non-real run as an AdvantageKit replay:

```java
if (isReal()) {
  // real robot receivers
} else {
  // locate and replay a WPILOG
}
```

This is useful for replay analysis, but it does not distinguish a normal physics simulation from a log replay. If both workflows are needed, add an explicit operating mode such as `REAL`, `SIM`, and `REPLAY`, then configure logging separately for each mode.

## Configuration Tests

`RobotConfigurationTest` checks important mistakes without constructing hardware or starting HAL. It verifies that:

- deploy configuration files contain valid JSON;
- the swerve drive references four distinct module files;
- Spark MAX IDs are in range and unique within the tested Spark devices;
- CANCoder IDs are in range and unique within the tested encoders; and
- every path referenced by a PathPlanner auto exists.

Run the same build used by CI:

```powershell
.\gradlew.bat build
```

Or run only the unit tests:

```powershell
.\gradlew.bat test
```

## Useful Test Improvements

The existing tests are a good start. The next useful additions would be:

- check CAN IDs across all device types on the same bus, including the gyro and intake;
- verify that every `Superstructure.Goal` has a command mapping;
- verify that the selected autonomous routine exists;
- load the YAGSL and PathPlanner configuration during a desktop test;
- test that scoring and idle request the expected child goals; and
- confirm that hardware constants such as current limits and soft limits remain within safe ranges.

## Continuous Integration

GitHub Actions runs `./gradlew build --no-daemon` on every pushed branch. After a successful build, it packages the tested robot JAR and deploy directory as an artifact. Main-branch builds become stable releases; other branches become preview releases.

This means a green CI run confirms compilation, unit tests, and packaging. It does **not** confirm motor direction, sensor calibration, mechanism safety, or behavior under load. Those still require simulation and controlled robot testing.

## Debugging a New Feature

When a feature does not work, trace it from the request down to the hardware:

1. Check that the controller trigger or autonomous event fired.
2. Check the logged superstructure or subsystem goal.
3. Check the currently scheduled command and subsystem requirements.
4. Compare the mechanism setpoint with its measured position or velocity.
5. Check motor output, current, and faults.
6. Confirm the CAN ID, inversion, gearing, and sensor configuration.

This order separates a scheduling problem from a controller-tuning or hardware problem. Avoid changing gains until the logs show that the expected command and setpoint reached the mechanism.

## Keeping These Docs Current

Update IronDocs in the same change whenever you modify:

- controller bindings;
- available subsystem or superstructure goals;
- CAN IDs, gearing, encoder offsets, or current limits;
- selected autonomous routines;
- logging and simulation modes; or
- the required build, test, and deploy workflow.

Documentation examples should describe code that exists in the repository. Clearly label planned designs and unfinished mechanisms so a new programmer can distinguish them from working behavior.
