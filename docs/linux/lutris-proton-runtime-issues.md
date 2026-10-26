# Lutris玩proton runtime游戏的一些设置

问题：启动就退出，code 256

原因：proton runtime依赖steam runtime，可通过设置STEAM\_**COMPAT**\_CLIENT_INSTALL_PATH这个环境变量解决

一般情况下，只要安装了steam，都可通过使用下列命令（启动lutris前，可写入.bashrc/.zshrc/.profile/.zprofile等文件永久生效）

```shell
STEAM_COMPAT_CLIENT_INSTALL_PATH=~/.local/share/Steam
```

实测，在lutris的游戏里设置该环境变量无效

问题：在同一个WINEPREFIX下启动的Cheat Engine读取不到内存

原因：需要额外的环境变量，将cheatengine.exe的完整路径设置为PROTON_REMOTE_DEBUG_CMD，此环境变量可以通过lutris的GUI中设置

```plaintext
PROTON_REMOTE_DEBUG_CMD="/home/myusername/.steam/steam/steamapps/Cheat_Engine_7.5/cheatengine.exe"
```

设置好后，启动游戏时会自动启动Cheat Engine，如果Cheat Engine退出了，需要重新启动游戏，参考：[https://wiki.cheatengine.org/index.php?title=Help\\\_File:Using\\\_Cheat\\\_Engine\\\_on\\\_Linux\\\_with\\\_Proton](https://wiki.cheatengine.org/index.php?title=Help%5C_File:Using%5C_Cheat%5C_Engine%5C_on%5C_Linux%5C_with%5C_Proton)