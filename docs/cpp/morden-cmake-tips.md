# 现代cmake使用技巧

不要再用nmake/make了，直接使用—build/—install吧，也不需要特意定义CMAKE_BUILD_TYPE，非常方便

```bash
#编译
cmake --build . --config Debug/Release
#安装，将安装到CMAKE_INSTALL_PREFIX下，默认是安装Release配置
cmake --install . --config Debug/Release
```

环境变量，设定好后就是全局可用，不会因为add_subdirectory而变化，在多级的子项目时尤其好用：

```bash
set(ENV{PROJ_BASE_DIR} ${CMAKE_CURRENT_SOURCE_DIR})
```

带缓存的PATH变量，可让Visual Studio/CMake GUI等工具有一个可选择文件夹路径的变量，并保存在缓存中，非常方便编辑：

```bash
set(OPENCV_DIR $ENV{OPENCV_ROOT} CACHE PATH "Root path of opencv")
    if(DEFINED OPENCV_DIR)
        list(APPEND CMAKE_PREFIX_PATH ${OPENCV_DIR})
    endif()
```

设置exe/dll/pdb文件输出路径：

```bash
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY $ENV{PROJ_OUTPUT_DIR}/out)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY $ENV{PROJ_OUTPUT_DIR}/out)
set(CMAKE_PDB_OUTPUT_DIRECTORY $ENV{PROJ_OUTPUT_DIR}/pdbs)
```

使用file命令的GLOB_RECURSE，子目录下的源文件也能一并扫描出来：

```bash
file(GLOB_RECURSE SRC_CPP ${PROJECT_SOURCE_DIR} *cpp)
```

使用target_compile_definitions给代码定义#define，子项目的DLL和EXE区别对待，其中，PRIVATE只应用于编译本项目时有效，PUBLIC应用于全局（所有子项目共享）：

```bash
#For DLL
target_compile_definitions(foo PRIVATE API_EXPORT_BUILD)
#For EXE
target_compile_definitions(bar PRIVATE API_EXPORT)
```