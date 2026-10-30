---
date: 2026-10-30
authors:
  - lee
categories:
  - C++
  - 编程
tages:
  - wxWidgets
  - CMake
---
# wxWidgets项目的CMake示例

```bash
cmake_minimum_required(VERSION 3.10)

set(proj TrayThem)

set(CMAKE_CXX_STANDARD 11)
project(${proj} CXX)

set(APP_NAME "Tray Them!")
set(APP_VERSION_MAJOR 0)
set(APP_VERSION_MINOR 0)
set(APP_VERSION_FIX 1)

configure_file(${CMAKE_SOURCE_DIR}/src/include/version.hpp.in ${CMAKE_SOURCE_DIR}/src/include/version.hpp)

file(GLOB proj_src ${CMAKE_SOURCE_DIR}/src/include/*.h
${CMAKE_SOURCE_DIR}/src/include/*.hpp
${CMAKE_SOURCE_DIR}/src/*.cpp
${CMAKE_SOURCE_DIR}/resources/app.rc)

#For windows, the root dir of wxwidgets and sqlite3 must be passed by command line, for example:
#cmake -DwxWidgets_ROOT_DIR=D:/Libraries/wxWidgets-3.0.5 -DwxWidgets_LIB_DIR=D:/Libraries/wxWidgets-3.0.5/lib/vc_x64_lib -DwxWidgets_CONFIGURATION=mswu -DSQLite3_INCLUDE_DIRS=D:/Libraries/sqlite3/include -DSQLite3_LIBRARIES=D:/Libraries/sqlite3/lib/x64 ..
if(WIN32)
message("===Start configure project on Windows===")
set(execOpt WIN32)
include_directories(${SQLite3_INCLUDE_DIRS})
link_directories(${SQLite3_LIBRARIES})
message("===End of configure project on Windows===")
endif()

find_package(wxWidgets COMPONENTS core base adv REQUIRED)
if(wxWidgets_USE_FILE)
include(${wxWidgets_USE_FILE})
else()
add_definitions(${wxWidgets_DEFINITIONS})
include_directories(${wxWidgets_INCLUDE_DIRS})
link_directories(${wxWidgets_LIBRARY_DIRS})
endif()

if(${CMAKE_VERSION}>="3.14")
find_package(sqlite3 REQUIRED)
endif()

add_executable(${proj} ${execOpt} ${proj_src})
target_link_libraries(${proj} PRIVATE ${wxWidgets_LIBRARIES})
target_link_libraries(${proj} PRIVATE sqlite3)
```