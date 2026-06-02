---
title: PID Control
---

# Introduction to PID

**Note** - Check WPILIB for more information (See [[basics]])

#### PID Loops

PID is a feedback loop, accounting for error that is already present. To regulate this, it essentially multiplies the *error* by the *gains*. Gains are constants, numbers tuned so that the PID loop produces the best output. There are three gains: P (Proportional), I (Integral), D (Derivative) - hence the **P** **I** **D**.

The **Proportional** gain does the heavy lifting. It is multiplied (ish) by the error, and is most of the PID's response to error.

**Integral** is not used in FRC. It tracks and accounts for error over time. We don't need that, because not much error builds up in the 20 seconds we run an auto for.

The final gain, **Derivative**, modulates P. This makes the loop slow down as it gets close to the setpoint, to try to account for overshot and *oscillation*. (See)

