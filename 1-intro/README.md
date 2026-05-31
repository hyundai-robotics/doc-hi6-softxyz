# 1. Overview

The SoftXYZ function is a **sensorless force control** feature that allows the robot to move compliantly in response to external forces based on a **Cartesian coordinate** frame within a user-defined environment.

To use this function accurately, the information for the **tool** mounted on the robot or any **additional payload** must be configured correctly.

Since this function operates on a **software-based** approach, it can be used **without any additional hardware**, such as force/torque sensors.

--- 

{% hint style="warning" %}

Since the SoftXYZ function is a **sensorless, force-sensor-free control feature**,  
there are **physical limitations** to achieving perfectly smooth and natural motion.

However, by appropriately adjusting the `softxyz_lim` parameters to match the working environment,  
it is possible to achieve motion that is as smooth as practicable.

The `softxyz_lim (pos / xnr / vel / thr)` values directly determine how the robot responds to external forces.  
Therefore, **fine-tuning is required** depending on factors such as the environment, assembly process, and tool rigidity.

{% endhint %}