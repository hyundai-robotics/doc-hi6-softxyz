# 2. 命令

SoftXYZ 功能通过两个命令进行配置和控制：`softxyz_lim` 和 `softxyz`。

- **`softxyz_lim`** 命令用于 **预定义 SoftXYZ 操作所需的基本限制参数**。
- **`softxyz`** 命令 **根据通过 `softxyz_lim` 配置的参数启用或禁用 SoftXYZ 功能**。

因此，要使用 SoftXYZ 功能，您必须首先使用 `softxyz_lim` 命令配置 **特定于轴的限制和操作范围**，然后使用 `softxyz on` 命令激活该功能。