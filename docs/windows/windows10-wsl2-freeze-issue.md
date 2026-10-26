# Windows 10+WSL2卡死处理

## 先看服务状态：

```bash
sc queryex LxssManager
```

如果STATE是STOP_PENDING，说明卡死，需要手动强杀 查到其中的PID字段，用任务管理器杀掉该进程 或使用命令 taskkill /PID 1234 /F 杀掉后，再检查状态，应访是RUNNING，这时候WSL又能启动了

## 优化项，可减少这种情况

### 禁用 metadata 同步优化（减少锁）

#### 编辑/etc/wsl.conf，加入

```plaintext
[automount] 
options = "metadata,umask=22,fmask=11
```

#### 更彻底的解决办法，更新WSL

```plaintext
wsl --update
```

如果Windows Update损坏，可从微软官方的WSL项目下载最新版本手动安装： <https://github.com/microsoft/WSL>

#### 升级后，可编辑/etc/wsl.conf启用systemd

```plaintext
[boot]
systemd=true
```