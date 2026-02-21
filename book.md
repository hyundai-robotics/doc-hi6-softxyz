
[__SOURCE](README.md)
# ${cont_model} 控制器功能描述 - SoftXYZ
[__SOURCE](0-about-this-manual/precautions.md)
# 注意事项

{% include url="https://hrcontentsrelay-bmgae5hdbzapc4bc.koreacentral-01.azurewebsites.net/api/proxy?path=doc-common-pages/zh/precautions.md" %}
[__SOURCE](1-intro/README.md)
# 1. 概述

SoftXYZ 功能是一种 **无传感器的力控制** 特性，允许机器人根据用户定义环境中的 **笛卡尔坐标** 框架，对外部力量做出顺应性移动。

要准确使用此功能，必须正确配置安装在机器人上的 **工具** 或任何 **额外负载** 的信息。

由于此功能基于 **软件** 方法，因此可以 **不需要任何额外硬件**，例如力/扭矩传感器。

---

## ⚠️ 注意事项

由于 SoftXYZ 功能是一种 **无传感器、无力传感器的控制特性**，  
因此实现完美平滑和自然运动存在 **物理限制**。

然而，通过适当地调整 `softxyz_lim` 参数以匹配工作环境，  
可以实现尽可能平滑的运动。

`softxyz_lim (pos / xnr / vel / thr)` 值直接决定机器人如何响应外部力量。  
因此，**需要根据环境、装配过程和工具刚性等因素进行微调**。
[__SOURCE](2-main/README.md)
# 2. 命令

SoftXYZ 功能通过两个命令进行配置和控制：`softxyz_lim` 和 `softxyz`。

- **`softxyz_lim`** 命令用于 **预定义 SoftXYZ 操作所需的基本限制参数**。
- **`softxyz`** 命令 **根据通过 `softxyz_lim` 配置的参数启用或禁用 SoftXYZ 功能**。

因此，要使用 SoftXYZ 功能，您必须首先使用 `softxyz_lim` 命令配置 **特定轴的限制和操作范围**，然后通过 `softxyz on` 命令激活该功能。
[__SOURCE](2-main/2.1-softxyz.md)
## 2.1 softxyz

一个允许机器人在未经传感器的情况下，由外部力量在笛卡尔坐标系中位移的功能。

<br>

### 语法

```python
softxyz on, crd=<参考坐标>
softxyz set, dpr=<刚度>
softxyz off
```
---

### 参数

- **on** : 启动 SoftXYZ 功能  
- **off** : 停止 SoftXYZ 功能  
- **set** : 修改 SoftXYZ 配置值  

- **crd** : 外部力响应的参考坐标框架  
  - 可用选项: `基础 (base)`, `机器人 (robot)`, `工具 (tool)`, `user_x`

- **dpr** : 刚度值  
  - 范围: **0.0 ~ 2.0**  
  - **更高的值增加刚度**，在外部力下造成的位移较小  
  - 默认值: **1.0**

```python
softxyz on,  crd="base"      # 基础坐标框架
softxyz on,  crd="robot"    # 机器人坐标框架
softxyz on,  crd="tool"     # 工具坐标框架
softxyz on,  crd="user_1"   # 用户定义的坐标框架 1

softxyz set, dpr=1.0        # 设置刚度值 (0.0-2.0, 更高 = 更加刚性)
softxyz off                 # 禁用该功能
```

<br>

> ✅ **信息**  
> - 在使用 `softxyz on` 之前，您 **必须** 使用 `softxyz_lim` 命令配置 `pos`、`xnr`、`vel` 和 `thr` 值。  
>   （设置最大位移、速度和笛卡尔阈值是强制性的。）
>
> - 为了提高对外部力量的敏感性，建议在执行 `softxyz on` 之前  
>   **使用延迟命令让机器人静止 1-2 秒**。
>
> - 如果在 SoftXYZ 操作期间发生振动，建议采取以下措施：
>   1) *增加 `thr` 值*
>   2) *增加 `dpr` 值*  
>   3) *减少 `vel` 值*
[__SOURCE](2-main/2.2-softxyz_lim.md)
## 2.2 softxyz_lim

The `softxyz_lim` command is used to **预配置参数值以在激活 `softxyz on` 功能之前**。 <br>

使用此命令，用户可以配置与机器人的笛卡尔行为相关的限制，包括 **最大位移**、**位置**、**速度**和 **阈值**。  

--- 
<br>

### 描述
* 配置 SoftXYZ 参数。 


### 语法
```python
softxyz_lim pos,_x=<+X_dist>,x_=<-X_dist>,_y=<+Y_dist>,y_=<-Y_dist>,_z=<+Z_dist>,z_=<-Z_dist>
softxyz_lim vel,x=<X_vel>,y=<Y_vel>,z=<Z_vel>,rx=<Rx_vel>,ry=<Ry_vel>,rz=<Rz_vel>
softxyz_lim xnr,x=<X_dist>,y=<Y_dist>,z=<Z_dist>,rx=<Rx_dist>,ry=<Ry_dist>,rz=<Rz_dist>
softxyz_lim thr,x=<X_thr>,y=<Y_thr>,z=<Z_thr>,rx=<Rx_thr>,ry=<Ry_thr>,rz=<Rz_thr>
```

### 参数

* **softxyz_lim pos** : 设置机器人在 X、Y 和 Z 方向上允许的最大笛卡尔位移。  
  单位: [mm]

* **softxyz_lim vel** : 设置机器人在操作期间在 X、Y、Z、Rx、Ry 和 Rz 方向上的最大笛卡尔速度。  
  单位: [mm/sec] 或 [deg/sec]

* **softxyz_lim xnr** : 限制机器人在 X、Y、Z、Rx、Ry 和 Rz 方向上的最大笛卡尔位移和旋转角度。  
  单位: [mm] 或 [deg] <br>  
  (机器人的最大操作工作空间由 **`pos` 和 `xnr` 限制的并集**决定。)

* **softxyz_lim thr** : 设置机器人在 X、Y、Z、Rx、Ry 和 Rz 方向上响应该外力开始移动所需的笛卡尔力/扭矩阈值。  
  单位: [N] 或 [Nm]

<br>

### 使用示例
> * 将最大允许位移设置为 +200 mm 在 +X 方向，-Y 方向为 100 mm，以及 +Z 方向为 300 mm。  
```python
softxyz_lim pos, _x=200, y_=100, _z=300
```
> * 将 Z 方向的最大笛卡尔速度设置为 40 mm/sec。
```python
softxyz_lim vel, z=40
```
> * 设置 X 方向上允许的运动范围从 -200 mm 到 +200 mm。
```python
softxyz_lim xnr, x=200
```
> * 将Y方向的笛卡尔力阈值设置为10 N。
```python
softxyz_lim thr, y=10
```
[__SOURCE](3-example/README.md)
# 3. 示例

本节提供 **代表性的配置和程序示例**，使用 `softxyz_lim` 和 `softxyz` 命令，帮助用户理解 SoftXYZ 功能的实际使用。

每个示例旨在基于关键参数设置演示 SoftXYZ 的行为，例如 **活动轴**、**运动范围**、**速度限制**和 **阈值**。  
这些示例的结构考虑了现实世界中的 **接触任务和力控制应用**。
[__SOURCE](3-example/3.1-example.md)
## 3.1 示例

* 该示例允许机器人在 X、Y 和 Ry 方向上移动，以在 Z 方向上执行装配操作。

<br>

> * 坐标框架：机器人坐标框架 (`crd="robot"`) <br>
> * 移动范围 (`xnr`) 限制：X 和 Y 方向在 [-50, +50] mm 之间，Ry 方向在 [-3, +3] deg 之间 <br>
> * 速度 (`vel`) 限制：X 和 Y 方向最大 5 mm/sec，Ry 方向最大 3 deg/sec <br>
> * 阈值 (`thr`) 限制：X 方向 3 N，Y 方向 3 N，以及 Ry 方向 1 Nm

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
## 3.2 示例

* 注塑件处理

> * 坐标框架：机器人坐标框架 (`crd="robot"`) <br>
> * 位置 (`pos`) 限制：在 +Y 方向上最多 300 毫米，在 -Y 方向上最多 200 毫米 <br>
> * 速度 (`vel`) 限制：Y 方向上最大合规速度为 150 毫米/秒 <br>

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