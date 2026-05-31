
[__SOURCE](README.md)
# ${cont_model} Controller Function Description - SoftXYZ

[__SOURCE](0-about-this-manual/precautions.md)
# Precautions

{% include file="en/precautions.md" %}

[__SOURCE](1-intro/README.md)
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
[__SOURCE](2-main/README.md)
# 2. Commands

The SoftXYZ function is configured and controlled using two commands: `softxyz_lim` and `softxyz`.

- The **`softxyz_lim`** command is used to **predefine the fundamental limit parameters** required for SoftXYZ operation.
- The **`softxyz`** command **enables or disables the SoftXYZ function** based on the parameters configured via `softxyz_lim`.

Therefore, to use the SoftXYZ function, you must first configure the **axis-specific limits and operational ranges** using the `softxyz_lim` command, and then activate the function using the `softxyz on` command.


[__SOURCE](2-main/2.1-softxyz.md)
## 2.1 softxyz

A function that allows the robot to be displaced by external forces in a Cartesian coordinate frame without using any sensors.

---

### Main Features
- **Sensorless Approach**  
  External force response is implemented purely through software without additional hardware
- **Cartesian Coordinate-Based Control**  
  External force handling is possible based on Base / Robot / Tool / User coordinate systems
- **Constraint-Based Compliance**  
  Safe push-back motion is performed only within predefined limits of distance, speed, and threshold values
---

### Syntax

```python
softxyz on, crd=<reference coordinate>
softxyz set, dpr=<stiffness>>
softxyz off
```
---

### Parameters

- **on** : Starts the SoftXYZ function  
- **off** : Stops the SoftXYZ function  
- **set** : Changes SoftXYZ configuration values  

- **crd** : Reference coordinate frame for external force response  
  - Available options: `base`, `robot`, `tool`, `user_x`

- **dpr** : Stiffness value  
  - Range: **0.0 ~ 2.0**  
  - A **higher value increases stiffness**, resulting in less displacement under external force  
  - Default value: **1.0**

```python
softxyz on,  crd="base"      # Base coordinate frame
softxyz on,  crd="robot"    # Robot coordinate frame
softxyz on,  crd="tool"     # Tool coordinate frame
softxyz on,  crd="user_1"   # User-defined coordinate frame 1

softxyz set, dpr=1.0        # Set stiffness value (0.0-2.0, higher = stiffer)
softxyz off                 # Disable the function
```
---

### Information  
- Before using `softxyz on`, you **must** configure the `pos`, `xnr`, `vel`, and `thr` values using the `softxyz_lim` command.  
   (Setting the maximum displacement, velocity, and Cartesian threshold values is mandatory.)

 - To increase sensitivity to external forces, it is recommended to  
   **keep the robot stationary for 1-2 seconds using a delay command** before executing `softxyz on`.

 - If vibration occurs during SoftXYZ operation, the following actions are recommended:
   1) *Increase the `thr` value*  
   2) *Increase the `dpr` value*  
   3) *Decrease the `vel` value*

[__SOURCE](2-main/2.2-softxyz_lim.md)
## 2.2 softxyz_lim

The `softxyz_lim` command is used to **preconfigure parameter values before activating the `softxyz on` function**. <br>

With this command, the user can configure limits related to the robot's Cartesian behavior, including **maximum displacement**, **position**, **velocity**, and **threshold values**.  

--- 

### Configuration Objectives
- **Limit the robot's response range** to external forces
- Prevent excessive displacement and velocity
- Implement **stable compliance behavior** suitable for the process and working environment

---

### Syntax
```python
softxyz_lim pos,_x=<+X_dist>,x_=<-X_dist>,_y=<+Y_dist>,y_=<-Y_dist>,_z=<+Z_dist>,z_=<-Z_dist>
softxyz_lim vel,x=<X_vel>,y=<Y_vel>,z=<Z_vel>,rx=<Rx_vel>,ry=<Ry_vel>,rz=<Rz_vel>
softxyz_lim xnr,x=<X_dist>,y=<Y_dist>,z=<Z_dist>,rx=<Rx_dist>,ry=<Ry_dist>,rz=<Rz_dist>
softxyz_lim thr,x=<X_thr>,y=<Y_thr>,z=<Z_thr>,rx=<Rx_thr>,ry=<Ry_thr>,rz=<Rz_thr>
```

### Parameters

* **softxyz_lim pos** : Sets the maximum allowable Cartesian displacement of the robot in the X, Y, and Z directions.  
  Unit: [mm]

* **softxyz_lim vel** : Sets the maximum Cartesian velocity of the robot during operation in the X, Y, Z, Rx, Ry, and Rz directions.  
  Unit: [mm/sec] or [deg/sec]

* **softxyz_lim xnr** : Limits the maximum Cartesian displacement and rotational angles of the robot in the X, Y, Z, Rx, Ry, and Rz directions.  
  Unit: [mm] or [deg] <br>  
  (The robot's maximum operating workspace is determined by the **union of the `pos` and `xnr` limits**.)

* **softxyz_lim thr** : Sets the Cartesian force/torque threshold required for the robot to start moving in response to external force, in the X, Y, Z, Rx, Ry, and Rz directions.  
  Unit: [N] or [Nm]

<br>

### Usage Examples
> * Sets the maximum allowable displacement to +200 mm in the +X direction, 100 mm in the -Y direction, and 300 mm in the +Z direction.  
```python
softxyz_lim pos, _x=200, y_=100, _z=300
```
> * Sets the maximum Cartesian velocity in the Z direction to 40 mm/sec.
```python
softxyz_lim vel, z=40
```
> * Sets the allowable movement range in the X direction from -200 mm to +200 mm.
```python
softxyz_lim xnr, x=200
```
> * Sets the Cartesian force threshold in the Y direction to 10 N.
```python
softxyz_lim thr, y=10
```

[__SOURCE](3-example/README.md)
# 3. Examples

This section provides **representative configuration and program examples** using the `softxyz_lim` and `softxyz` commands to help users understand the practical usage of the SoftXYZ function.

Each example is designed to demonstrate the behavior of SoftXYZ based on key parameter settings, such as **active axes**, **movement ranges**, **velocity limits**, and **threshold values**.  
The examples are structured with consideration for real-world **contact tasks and force-control applications**.

[__SOURCE](3-example/3.1-example.md)
## 3.1 Example - Z direction assembly compliance settings

* Example where the robot is allowed to be displaced in the X, Y, and Ry directions in order to perform an assembly operation along the Z direction.

<br>

> * Coordinate frame: Robot coordinate frame (`crd="robot"`) <br>
> * Movement range (`xnr`) limits: X and Y directions within [-50, +50] mm, Ry direction within [-3, +3] deg <br>
> * Velocity (`vel`) limits: Maximum 5 mm/sec in the X and Y directions, and 3 deg/sec in the Ry direction <br>
> * Threshold (`thr`) limits: 3 N in the X direction, 3 N in the Y direction, and 1 Nm in the Ry direction

```python
S1   move P, spd=100mm/sec, accu=0, tool=0
     delay 2.0   # Delay is required before executing softxyz on
     softxyz_lim xnr, x=50, y=50, ry=3
     softxyz_lim vel, x=5, y=5, ry=3
     softxyz_lim thr, x=20, y=20, ry=3
     softxyz on, crd="robot"

S2   move P, spd=250mm/sec, accu=0, tool=0
     softxyz off
     end 
```

[__SOURCE](3-example/3.2-example.md)
## 3.2 Example - Injection molded part handling

* Injection-molded part handling

> * Coordinate frame: Robot coordinate frame (`crd="robot"`) <br>
> * Position (`pos`) limits: Up to 300 mm in the +Y direction and up to 200 mm in the -Y direction <br>
> * Velocity (`vel`) limits: Maximum compliant velocity of 150 mm/sec in the Y direction <br>

```python
S1   move P, spd=100mm/sec, accu=0, tool=0
     delay 2.0   # Delay is required before executing softxyz on
     softxyz_lim pos, _y=300, y_=200
     softxyz_lim vel, y=150
     softxyz on, crd="robot"

S2   wait ...
     softxyz off
     end
```
