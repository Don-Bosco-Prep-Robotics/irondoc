---
sidebar_label: 'Controlling a Spark Max'
sidebar_position: 1
---

# Controlling a Spark Max
*See https://docs.revrobotics.com/revlib*  
We use SparkMax motor controllers  

![Spark Max](./img/sparkmax.png)

They are made by REV, and interface very well with Rev's other hardware, ie, the NEO. They must be configured for each individual case. There are two ways to do this.  
**ONE** is via the *REV Hardware Client*. This is Rev's application for controlling, managing, and configuring Rev hardware. See [The Basics](../basics)

To use, just plug a USB-A to USB-C cable into the laptop and Spark Max.
:::tip 
If the spark shows up as "Outdated Rev Device", this means that it is running pre-2026 firmware. To fix, unplug it and hold down the button on top while plugging it in again. It should show up as a Recovery Device. Then you can update the firmware to 2026.
:::

**ALTERNATIVELY** you can configure directly from code.
#### TODO - image here