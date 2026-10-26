# Windows下编译Qt6.5

## 编译环境

操作系统： Windows 11 Home 22H2

编译器：Visual Studio 2022 Community

CPU：Intel Core i7-12700 @ 2.10GHz

内存：16GB

参考官方文档： <https://wiki.qt.io/Building_Qt_6_from_Git> <https://doc.qt.io/qt-6.5/qtwebengine-platform-notes.html#windows> <https://wiki.qt.io/QtWebEngine/Qt6Build>

## 准备工作及依赖

编译Qt6所需依赖：

- Git (>= 1.6.x)
- CMake (>= 3.16, >= 3.18.4 for Ninja Multi-Config, >= 3.19 for WebEngine, >= 3.21.1 for static Qt builds in Qt 6.2+, or builds for Apple platforms in Qt 6.6+)
- Ninja
- C++ compiler supporting C++ 17
- Perl (>=5.14, optional for Qt >= 6.5)
- Python (>=2.6.x)
- libclang (>=10, optional when QDoc should be built, prebuilt versions for each OS can be downloaded here or installed through the libclang-dev package on Linux)
- Visual Studio 2022, Visual Studio 2019, MinGW 11.2
- 如果需要 Make 本地文档，则需要安装 LLVM ，而且需要的是 Qt Company 预构建的 LLVM 。LLVM Qt Prebuilt: <https://download.qt.io/development_releases/prebuilt/libclang/qt/> 编译WebEngine所需依赖：
- CMake 3.19 or newer
- Python 3 with html5lib library，由于使用了imp库的关系不支持3.12，最高可用3.11
- Bison, Flex，偷懒的话，可以从qt5的源码中copy，自己下的话记得要手动处理libiconv和libintl的依赖，确保命令行下不报错就行
- GPerf
- Node.js version 12 or later，用最后的12.x版本即可
- OpenSSL，可从源代码安装

```bash
perl Configure VC-WIN64A --prefix=D:\Libraries\OpenSSL\3.0.12
nmake
nmake install
```

我自己写了个bat把环境变量都设置好，放在解压后的源码目录运行即可：

```bash
set _ROOT=%cd%
set PATH=%_ROOT%/qtbase/bin;%PATH%
set PATH=%PATH%;D:/Tools/bison-2.4.1-bin/bin
set PATH=%PATH%;D:/Tools/flex-2.5.4a-1-bin/bin
set PATH=%PATH%;D:/Tools/gperf-3.0.1-bin/bin
SET PATH=%_ROOT%/qtrepotools/bin;%PATH%
SET PATH=%PATH%;C:/Strawberry/perl/bin
set LLVM_INSTALL_DIR="C:/Program Files/LLVM/bin"
set OPENSSL_ROOT_DIR=D:/Libraries/OpenSSL/3.0.12
```

## 开始编译

必须使用x64 Native Tools Command Prompt for Visual Studio 2012运行以下命令 运行env.bat设置path后，使用以下配置： 注意事项：

- 需使用`chcp 65001`切换至utf-8 code page开始编译，否则后续webengine中的python脚本会报错
- 需提前将OpenSSL的两个DLL复制到qtbase/bin中，后续过程有依赖
- Qt6无法同时编译debug和release版本，需删除CMakeCache.txt后，再重新configure 编译debug版本：

```bash
configure -opensource -confirm-license -verbose -debug -webengine-proprietary-codecs -nomake tests -nomake examples -prefix D:/Libraries/Qt/6.5.3/msvc2022_x64 -mp -opengl dynamic -openssl-linked OPENSSL_PREFIX=D:/Libraries/OpenSSL/3.0.12 -I D:/Libraries/OpenSSL/3.0.12/include -L D:/Libraries/OpenSSL/3.0.12/lib OPENSSL_LIBS="-lUser32 -lAdvapi32 -lGdi32 -llibcrypto -llibssl"
```

编译release版本：

```bash
configure -opensource -confirm-license -verbose -release -webengine-proprietary-codecs -nomake tests -nomake examples -prefix D:/Libraries/Qt/6.5.3/msvc2022_x64 -mp -opengl dynamic -openssl-linked OPENSSL_PREFIX=D:/Libraries/OpenSSL/3.0.12 -I D:/Libraries/OpenSSL/3.0.12/include -L D:/Libraries/OpenSSL/3.0.12/lib OPENSSL_LIBS="-lUser32 -lAdvapi32 -lGdi32 -llibcrypto -llibssl"
```

如果之前编译过不支持ssl的版本，需要把文件删掉重新编译 使用cmake进行并行编译（实际调用的ninja）：

```bash
cmake --build . --parallel
```

时间主要花在编译webengine上 在生成文档前，必须先安装，记得将OpenSSL的几个DLL复制到qt安装的路径的bin目录下

```bash
ninja install
```

或者使用cmake方式安装

```bash
cmake --install .
```

生成文档，此操作需先install后，再将qdoc的路径加入至PATH，可先单独运行一下qdoc看是否报错，如报错，需将libclang.dll复制到qt安装的路径的bin目录下

```bash
cmake --build . --target docs
```

安装文档

```bash
cmake --build . --target install_docs
```

整个过程很长很痛苦，主要是webengine要编译一个完整的chromium，太久了