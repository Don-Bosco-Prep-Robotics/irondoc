---
title: Pathplanner
---

# How To Use Pathplanner

## What is Pathplanner?

Pathplanner is an autonomous framework. For thier own explanation, see [the docs](https://pathplanner.dev/home.html).

### Configuring Autobuilder, Expanded;

The pathplanner docs page on this is pretty good. I would offer a few small explanations.  

There are, as stated, 4 functions that the Autobuilder is given.  

- `getPose()` - This returns the robot's current pose, from the pose estimator in `RobotState`
- `resetPose()` - Resets the pose estimation to the inputed pose. This is *very* helpful for autos because this means that movement can all be start-position relative.
- `getCurrentSpeeds` 
    - Returns the robot's current **Robot Relative** ChassisSpeeds. Conversion uses a `DifferentialDriveWheelSpeeds` and then uses the `DifferentialDriveKinematics` to convert it to `ChassisSpeeds`
- `runClosedLoop()` 
    - This one is worth talking about. This outputs two wheelspeeds, for the left and right sides of the drivetrain. This should be fed to a closed loop controller, to minimze error and maintain ideal pathing.

