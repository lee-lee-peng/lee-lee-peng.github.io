---
date: 2026-10-30
authors:
  - lee
categories:
  - C++
  - 编程
  - Windows
tages:
  - Qt
---
# 解决QWidget用winId获取HWND而导致Qt程序无事件消息的问题

问题原因：

当对一个QWidget调用winId时，默认情况下，Qt会对该窗口进行Native化，从而导致如鼠标等事件被其它原生窗口接管，表现出来的现像就是窗口不响应任何事件

解决思路：

防止Qt窗口Native化

解决办法：

第一步，在创建QApplication对象之前设置：

```cpp
QApplication::setAttribute(Qt::AA_DontCreateNativeWidgetSiblings);
//注意：Qt::AA_NativeWindows受环境变量 QT_USE_NATIVE_WINDOWS控制，
//有可能环境变量被别的软件修改，所以也需要设置下
QApplication::setAttribute(Qt::AA_NativeWindows,false); 
```

第二步，在调用winId之前对该QWidget设置：

```cpp
this->setAttribute(Qt::WA_DontCreateNativeAncestors);
```