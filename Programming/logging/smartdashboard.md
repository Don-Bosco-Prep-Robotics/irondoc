---
sidebar_label: 'Smart Dashboard'
sidebar_position: 1
---


# Smart Dashboard

Smart Dashboard will be deprecated in 2027. Until then, it is one of the easiest ways to log values to Network Tables. Behold:
```jsx
SmartDashboard.putNumber("Robot Speed ms", robotSpeed);
```

This is called in the `periodic()` function. It is updated every cycle. This then shows up in Network Tables under Smart Dashboard. To put it in a subcategory, do as follows:  

```jsx
SmartDashboard.putNumber("Drivetrain/Robot Speed MS", robotSpeed);
```

Done. This is very nice and simple.