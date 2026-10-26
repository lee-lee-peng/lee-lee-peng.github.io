#FreeBSD作为VirtualBox虚拟机时鼠标滚轮有问题时的处理方法

1、屏蔽掉按键8的mapping（不同人可能按键不一样）

先查看mapping（0表示屏蔽）：

```bash
xmodmap -pp
```

```bash
There are 12 pointer buttons defined.

    Physical        Button
     Button          Code
        1              1
        2              2
        3              3
        4              4
        5              5
        6              6
        7              7
        8              0
        9              9
       10             10
       11             11
       12             12
```

例子中有12个按键

使用以下代码屏蔽掉按键8

```bash
xmodmap -e "pointer = 1 2 3 4 5 6 7 0 9 10 11 12"
```

然后再开启一个pcmanfm之类的窗口测试滚动，如果不行，换别的按键

测试成功后，将以下内容保存至~/.xmodmap

```bash
pointer = 1 2 3 4 5 6 7 0 9 10 11 12
```

更新.profile，加入xmodmap命令，确保每次登陆时，都会执行该动作

```bash
/usr/local/bin/xmodmap -e "pointer = 1 2 3 4 5 6 7 0 0 0 11 12"
```

2、Firefox滚动有问题的处理方法

输入about:config，查找mousebutton，找到mousebutton.4th.enabled，双击改为false即可

3、使用xinput和xev调整按键mapping

先通过xinput list找到对应鼠标的device Id

然后使用xinput get-button-map \[devide_id\]取到对应设备的mapping（0表示屏蔽），如：

```bash
1 2 3 4 5 6 7 8 9 10 11 12
```

使用xev打开一个窗口，在该窗口点击鼠标各按键，确认所需按键

使用xinput set-button-map \[device id\] \[按键mapping\]设置mapping，如：

```bash
xinput set-button-map 7 1 2 3 4 5 0 0 0 0 0 0 0
```

该操作可加入至.profile中以免下次登陆失效

4、终级方式，禁用Host/Guest鼠标平滑切换（禁用后，需使用Right Ctrl切换鼠标）

使用`xinput list`查看设备，找到nonamed或virtualbox相关的设备

通过`xinput get-button-map [device id]`查看mapping，确认为如下mapping：

1 0

使用`xinput disable [device id]`将该设备禁用即可