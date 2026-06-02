---
title: Tuning PID
---

# Tuning PID Loops

PID can be very tricky to tune. There is no perfect system, because no two systems are exactly the same. This is a *best practice*.
Also see the WPILIB page on tuning PID for more information - https://docs.wpilib.org/en/2020/docs/software/advanced-control/introduction/tuning-pid-controller.html

## PID Tuning Best Practice

> **Warning:** Put it on the cart first. It will do crazy things, don't let it do them to the lab.

### Order of Tuning

> **Tip:** Tuning should be done via exponential search. Increase by a factor of x2 each increase. If it overshoots, reduce to the mean of the current value and the previous value.

Tune the P gain first. Start with a small value relative to your units. Don't let it go haywire. Increase P until it reaches the setpoint over time (ish) and starts to oscillate. Then reduce P and increase D.