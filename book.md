
[__SOURCE](README.md)
# ${cont_model} 控制器功能描述 - SoftXYZ
[__SOURCE](0-about-this-manual/README.md)
# 关于手册
[__SOURCE](0-about-this-manual/precautions.md)
# 注意事项

{% include file="zh/precautions.md" %}
[__SOURCE](0-about-this-manual/safety-notice.md)
# 安全注意事项

{% include file="zh/safety-notice.md" %}
[__SOURCE](1-intro/README.md)
# 1. 概述

SoftXYZ 功能是一个 **无传感器力控制** 特性，允许机器人在用户定义的环境中，根据 **笛卡尔坐标** 框架对外部力做出柔顺移动。

要准确使用此功能，必须正确配置安装在机器人上的 **工具** 或任何 **附加负载** 的信息。

由于此功能基于 **软件实现**，因此可以 **不需要任何额外硬件**，如力/扭矩传感器。

---

{% hint style="warning" %}

由于 SoftXYZ 功能是一个 **无传感器、无力传感器的控制特性**，  
因此在实现完全平滑和自然运动方面存在 **物理限制**。

然而，通过适当调整 `softxyz_lim` 参数以匹配工作环境，  
可以实现尽可能平滑的运动。

`softxyz_lim (pos / xnr / vel / thr)` 值直接决定了机器人如何响应外部力。  
因此，**需要根据环境、组装过程和工具刚性等因素进行微调**。

{% endhint %}
[__SOURCE](2-main/README.md)
# 2. 命令

SoftXYZ 功能通过两个命令进行配置和控制：`softxyz_lim` 和 `softxyz`。

- **`softxyz_lim`** 命令用于 **预定义 SoftXYZ 操作所需的基本限制参数**。
- **`softxyz`** 命令 **根据通过 `softxyz_lim` 配置的参数启用或禁用 SoftXYZ 功能**。

因此，要使用 SoftXYZ 功能，您必须首先使用 `softxyz_lim` 命令配置 **特定于轴的限制和操作范围**，然后使用 `softxyz on` 命令激活该功能。
[__SOURCE](2-main/2.1-softxyz.md)
## 2.1 softxyz

允许机器人在笛卡尔坐标系中被外部力量位移而不使用任何传感器的功能。

---

### 主要特点
- **无传感器方法**  
  外部力响应完全通过软件实现，无需额外硬件
- **基于笛卡尔坐标的控制**  
  基于基座/机器人/工具/用户坐标系统处理外部力量
- **基于约束的一致性**  
  仅在预定义的距离、速度和阈值限制内执行安全的推回动作
---

### 语法

```python
softxyz on, crd=<reference coordinate>
softxyz set, dpr=<stiffness>>
softxyz off
```
---

### 参数

- **on** : 启动SoftXYZ功能  
- **off** : 停止SoftXYZ功能  
- **set** : 更改SoftXYZ配置值  

- **crd** : 外部力响应的参考坐标系  
  - 可用选项：`基座 (base)`, `机器人 (robot)`, `工具 (tool)`, `user_x`

- **dpr** : 刚度值  
  - 范围：**0.0 ~ 2.0**  
  - **更高的值增加刚度**，在外部力作用下位移更小  
  - 默认值：**1.0**

```python
softxyz on,  crd="base"      # 基座坐标系
softxyz on,  crd="robot"    # 机器人坐标系
softxyz on,  crd="tool"     # 工具坐标系
softxyz on,  crd="user_1"   # 用户定义坐标系1

softxyz set, dpr=1.0        # 设置刚度值（0.0-2.0，更高=更硬）
softxyz off                 # 禁用功能
```
---

### 信息  
- 使用 `softxyz on` 之前，您 **必须** 通过 `softxyz_lim` 命令配置 `pos`、`xnr`、`vel` 和 `thr` 值。  
   （设置最大位移、速度和笛卡尔阈值是强制性的。）

 - 为了提高对外部力量的敏感性，建议  
   **在执行 `softxyz on` 之前通过延迟命令让机器人静止1-2秒。**

 - 如果在SoftXYZ操作期间发生振动，建议采取以下措施：
   1) *增加`thr`值*  
   2) *增加`dpr`值*  
   3) *减少`vel`值*
[__SOURCE](2-main/2.2-softxyz_lim.md)
## 2.2 softxyz_lim

The `softxyz_lim` command is used to **预先配置参数值，以激活 `softxyz on` 功能**。<br>

With this command, the user can configure limits related to the robot's Cartesian behavior, including **最大位移**, **位置**, **速度**, and **阈值**。

---

### Configuration Objectives
- **限制机器人对外部力量的响应范围**
- 防止过度位移和速度
- 实施**适合过程和工作环境的稳定顺应行为**

---

### Syntax
```python
softxyz_lim pos,_x=<+X_dist>,x_=<-X_dist>,_y=<+Y_dist>,y_=<-Y_dist>,_z=<+Z_dist>,z_=<-Z_dist>
softxyz_lim vel,x=<X_vel>,y=<Y_vel>,z=<Z_vel>,rx=<Rx_vel>,ry=<Ry_vel>,rz=<Rz_vel>
softxyz_lim xnr,x=<X_dist>,y=<Y_dist>,z=<Z_dist>,rx=<Rx_dist>,ry=<Ry_dist>,rz=<Rz_dist>
softxyz_lim thr,x=<X_thr>,y=<Y_thr>,z=<Z_thr>,rx=<Rx_thr>,ry=<Ry_thr>,rz=<Rz_thr>
```

### Parameters

* **softxyz_lim pos** : 设置机器人在 X, Y 和 Z 方向上允许的最大笛卡尔位移。  
  Unit: [mm]

* **softxyz_lim vel** : 设置机器人在 X, Y, Z, Rx, Ry 和 Rz 方向上的最大笛卡尔速度。  
  Unit: [mm/sec] or [deg/sec]

* **softxyz_lim xnr** : 限制机器人在 X, Y, Z, Rx, Ry 和 Rz 方向上的最大笛卡尔位移和旋转角度。  
  Unit: [mm] or [deg] <br>  
  (机器人的最大操作工作空间由 **`pos` 和 `xnr` 限制的联合决定**。)

* **softxyz_lim thr** : 设置机器人在 X, Y, Z, Rx, Ry 和 Rz 方向上响应外部力量时所需的笛卡尔力/扭矩阈值。  
  Unit: [N] or [Nm]

<br>

### Usage Examples
> * 设置在 +X 方向上的最大允许位移为 +200 mm，在 -Y 方向上的最大位移为 100 mm，在 +Z 方向上的最大位移为 300 mm。  
```python
softxyz_lim pos, _x=200, y_=100, _z=300
```
> * 设置在 Z 方向上的最大笛卡尔速度为 40 mm/sec。
```python
softxyz_lim vel, z=40
```
> * 设置在 X 方向上允许的运动范围为 -200 mm 到 +200 mm。
```python
softxyz_lim xnr, x=200
```
> * 设置在 Y 方向上的笛卡尔力阈值为 10 N。
```python
softxyz_lim thr, y=10
```
[__SOURCE](3-example/README.md)
# 3. 示例

此部分提供 **使用 `softxyz_lim` 和 `softxyz` 命令的代表性配置和程序示例**，以帮助用户理解 SoftXYZ 功能的实际使用。

每个示例旨在基于关键参数设置，如 **活动轴**、**运动范围**、**速度限制** 和 **阈值**，展示 SoftXYZ 的行为。  
这些示例的结构考虑了现实世界的 **接触任务和力控制应用**。
[__SOURCE](3-example/3.1-example.md)
## 3.1 示例 - Z 方向装配合规设置

* 示例中，机器人被允许在 X、Y 和 Ry 方向上移位，以便沿 Z 方向执行装配操作。

<br>

> * 坐标框架：机器人坐标框架 (`crd="robot"`) <br>
> * 移动范围 (`xnr`) 限制：X 和 Y 方向在 [-50, +50] mm 范围内，Ry 方向在 [-3, +3] deg 范围内 <br>
> * 速度 (`vel`) 限制：X 和 Y 方向最大 5 mm/sec，Ry 方向最大 3 deg/sec <br>
> * 阈值 (`thr`) 限制：X 方向 3 N，Y 方向 3 N，Ry 方向 1 Nm

```python
S1   move P, spd=100mm/sec, accu=0, tool=0
     delay 2.0   # 在执行 softxyz 前需要延迟
     softxyz_lim xnr, x=50, y=50, ry=3
     softxyz_lim vel, x=5, y=5, ry=3
     softxyz_lim thr, x=20, y=20, ry=3
     softxyz on, crd="robot"

S2   move P, spd=250mm/sec, accu=0, tool=0
     softxyz off
     end 
```
[__SOURCE](3-example/3.2-example.md)
## 3.2 示例 - 注塑件处理

* 注塑件处理

> * 坐标框架：机器人坐标框架 (`crd="robot"`) <br>
> * 位置 (`pos`) 限制：在 +Y 方向最多 300 mm，在 -Y 方向最多 200 mm <br>
> * 速度 (`vel`) 限制：在 Y 方向最大合规速度为 150 mm/sec <br>

```python
S1   move P, spd=100mm/sec, accu=0, tool=0
     delay 2.0   # 在执行 softxyz 之前需要延迟
     softxyz_lim pos, _y=300, y_=200
     softxyz_lim vel, y=150
     softxyz on, crd="robot"

S2   wait ...
     softxyz off
     end
```