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
# Widnows下编译Qt5.15.8

## 编译环境

操作系统： Windows 10 Professional 22H2

编译器：Visual Studio 2019 Community CPU：Intel Core i5-8300H @ 2.3GHz

内存：16GB

参考官方文档： <https://doc.qt.io/qt-5/windows-building.html> <https://doc.qt.io/qt-6/qtwebengine-platform-notes.html>

## 准备工作及依赖

编译Qt5.15.8依赖现在基本都包含在源代码里了：

- 在qtbase\\bin和gnuwin32\\bin中
- CMake需要自己另行安装，并加入至path环境变量中
- 如果需要 Make 本地文档，则需要安装 LLVM WebEngine所需依赖：
- Python 2.7.5，不支持Python3，此处有坑，不支持2.7.10以上版本，最好就用Python2.7.5，另外，如果path里有python3 ,要去掉。版本正确，还找不到python2的话，copy一份python.exe为python2.exe
- Bison, Flex，已经在源码自带的gnuwin32\\bin中
- GPerf，需自行安装
- Node.js version 8 or later (version 12 or later is recommended)，就用12.x就好，太新的不要
- OpenSSL 3.0.x 更高版本的不行

```bash
perl Configure VC-WIN64A --prefix=D:\Libraries\OpenSSL\3.0.9
nmake
nmake install
```

我自己写了个bat把环境变量都设置好，放在解压后的源码目录运行即可：

```bash
set _ROOT=%cd%
set PATH=%_ROOT%\qtbase\bin;%_ROOT%\gnuwin32\bin;%PATH%
set PATH=%PATH%;C:\Python27
set PYTHONPATH=C:\Python27\DLLs
set PATH=%PATH%;D:\Tools\win_flex_bison
set PATH=%PATH%;D:\Tools\gperf\bin
set PATH=%PATH%;D:\Tools\nasm
set PATH=%PATH%;D:\Tools\ruby-1.9.3-p551-i386-mingw32\bin
set OPENSSL_PREFIX=D:\Libraries\OpenSSL\3.0.9
set PATH=%PATH%;%OPENSSL_PREFIX%\bin
set INCLUDE=%INCLUDE%;%OPENSSL_PREFIX%\include
set LIB=%LIB%;%OPENSSL_DIR%\lib
SET PATH=%_ROOT%\qtrepotools\bin;%PATH%
set LLVM_INSTALL_DIR=C:\Program Files\LLVM
```

## 开始编译

必须使用x64 Native Tools Command Prompt for Visual Studio 2019运行以下命令 运行env.bat设置path后，使用以下配置：

```bash
configure -recheck-all -opensource -confirm-license -verbose -debug-and-release -webengine-proprietary-codecs -nomake tests -nomake examples -prefix D:\Libraries\Qt\5.15.9\msvc2019_x64 -mp -opengl dynamic -openssl-linked OPENSSL_PREFIX=D:\Libraries\OpenSSL\3.0.9 -I D:\Libraries\OpenSSL\3.0.9\include -L D:\Libraries\OpenSSL\3.0.9\lib OPENSSL_LIBS="-lUser32 -lAdvapi32 -lGdi32 -llibcrypto -llibssl"
```

如果之前编译过不支持ssl的版本，需要把文件删掉重新编译 使用jom代替nmake进行并行编译：

```bash
jom -j16
```

整个过程大约10小时，主要花在编译webengine上 在生成文档前，必须先安装，记得将OpenSSL的几个DLL复制到qt安装的路径的bin目录下

```bash
jom install
```

生成文档，此操作需先install后，再将qdoc的路径加入至PATH，可先单独运行一下qdoc看是否报错，如报错，需将libclang.dll复制到qt安装的路径的bin目录下

```bash
jom docs
```

安装文档

```bash
jom install_docs
```

整个过程很长很痛苦，主要是webengine要编译一个完整的chromium，太久了