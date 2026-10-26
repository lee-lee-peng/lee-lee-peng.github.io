# Windows 10机械硬盘（HD）使用率100%的解决方法

原因：Sysmain服务导致

解决方法：

1. 将虚拟内存（分页文件大小）从默认的”系统管理的大小“设置为固定的大小，最大和最小大小保持一致
2. 关闭且禁用Sysmain服务（过去叫superfetch）

使用管理员权限打开CMD（或用`services.msc`打开服务管理器关闭后禁用Sysmain）：

```bash
sc stop "SysMain" & sc config "SysMain" start=disabled
```

3. 禁用Edge更新

需禁用的服务有：

- MicrosoftEdgeElevationService
- edgeupdate
- edgeupdatem

需禁用的计划任务有：

- MicrosoftEdgeUpdateTaskMachineCore
- MicrosoftEdgeUpdateTaskMachineUA

4. 可以考虑禁用Windows Update服务