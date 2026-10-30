---
date: 2026-10-30
authors:
  - lee
categories:
  - C++
  - 编程
tages:
  - Qt
  - CMake
---
# cmake管理使用了qt的项目的正确使用方法

可将QT5_DIR（包括了bin/inclue/lib等目录的那个基础目录，不是lib/cmake）加到CMAKE_PREFIX_PATH中，防止find_package无法使用

cmake对qt moc有如下几个函数封装：

qt_wrap_ui(\[输出\]MOC后的源文件列表 \[输入\]MOC前的.ui文件)

qt_wrap_cpp(\[输出\]MOC后的源文件列表 \[输入\]MOC前的源文件，通常是包含了Q_OBJECT的.hpp)

qt_add_resources(\[输出\]MOC后的源文件列表 \[输入\]MOC前的.qrc文件)

经过这三步后，就可以将moc后在源文件add_exectuable/add_library了

示例代码：

```bash
#让cmake能找到qt，防止find_package无法使用
list(APPEND CMAKE_PREFIX_PATH ${QT5_DIR})

#将使用Designer编辑好的.ui文件处理为C++源文件
file(GLOB UI_SRC ui/*.ui)
qt_wrap_ui(MOC_UI_SRC ${UI_SRC})

#将带有如Q_OBJECT等Meta-Object的头文件处理为moc后的C++头/源文件
file(GLOB FORM_SRC include/ui/*.hpp)
qt_wrap_cpp(MOC_FORM_SRC ${FORM_SRC})

#将Qt资源文件(*.qrc)编译为C++源文件
file(GLOB FORM_QRC ui/*.qrc)
qt_add_resources(MOC_FORM_QRC ${FORM_QRC})

#正确使用find_package
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)

#处理完后的源码作为exe/lib的输入
add_exectuable(example ${MOC_UI_SRC}
${MOC_FORM_SRC}
${FORM_QRC})

#链接Qt库，该命令会自动将Qt5对应的的include目录加入至include directories
target_link_libraries(example PRIVATE Qt5::Widgets)
```