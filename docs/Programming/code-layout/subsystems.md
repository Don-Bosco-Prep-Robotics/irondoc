---
sidebar_label: 'Subsystems'
sidebar_position: 3
---

# Subsystems

*See WPILIB Docs - https://docs.wpilib.org/en/stable/docs/software/commandbased/subsystems.html*

What follows is a summary of the above page

A subsystem is an abstraction for a collection of hardware that operates as a unit, such as a drivetrain or an intake. We do this in a couple of ways, depending on the kind of subsystem. For example, a drivetrain. Drivetrains tend to expose public methods like `setVelocity()`. These tend to call on whatever IO Layer was provided.

Ocassionaly, in other code layouts, and indeed the WPILIB docs, subsystems implement Commands inside themselves.