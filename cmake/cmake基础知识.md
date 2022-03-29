# Cmake常见命令

# CMake的常用变量和函数

先将这些函数和变量罗列出来，便于之后查找

## 变量

| Variable                                                     | Info                                                       |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| CMAKE_SOURCE_DIR                                             | 根源代码目录，工程顶层目录。暂认为就是PROJECT_SOURCE_DIR   |
| CMAKE_CURRENT_SOURCE_DIR                                     | 当前处理的 CMakeLists.txt 所在的路径                       |
| CMAKE_SOURCE_DIR, PROJECT_SOURCE_DIR, `<projectname>`_SOURCE_DIR | 工程顶层目录                                               |
| CMAKE_BINARY_DIR                                             | 运行cmake的目录。外部构建时就是build目录                   |
| CMAKE_CURRENT_BINARY_DIR                                     | The build directory you are currently in.当前所在build目录 |
| PROJECT_BINARY_DIR                                           | 暂认为就是CMAKE_BINARY_DIR                                 |
| EXECUTABLE_OUTPUT_PATH , LIBRARY_OUTPUT_PATH                 | 最终目标文件存放的路径。                                   |
| PROJECT_NAME                                                 | 通过 PROJECT 指令定义的项目名称。                          |
| CMAKE_MAJOR_VERSION                                          | CMAKE 主版本号,比如 2.4.6 中的 2                           |
| CMAKE_MINOR_VERSION                                          | CMAKE 次版本号,比如 2.4.6 中的 4                           |
| CMAKE_PATCH_VERSION                                          | CMAKE 补丁等级,比如 2.4.6 中的 6                           |
| CMAKE_SYSTEM                                                 | 系统名称,比如 Linux-2.6.22                                 |
| CMAKE_SYSTEM_NAME                                            | 不包含版本的系统名,比如 Linux                              |
| CMAKE_SYSTEM_VERSION                                         | 系统版本,比如 2.6.22                                       |
| CMAKE_SYSTEM_PROCESSOR                                       | 处理器名称,比如 i686.                                      |
| UNIX                                                         | 在所有的类 UNIX 平台为 TRUE,包括 OS X 和 cygwin            |
| WIN32                                                        | 在所有的 win32 平台为 TRUE,包括 cygwin                     |
| BUILD_SHARED_LIBS                                            | 使用 `ADD_LIBRARY` 时生成动态库                            |
| BUILD_STATIC_LIBS                                            | 使用 `ADD_LIBRARY` 时生成静态库                            |
| CMAKE_C_FLAGS                                                | 设置 C 编译选项,也可以通过指令 ADD_DEFINITIONS()添加。     |
| CMAKE_CXX_FLAGS                                              | 设置 C++编译选项,也可以通过指令 ADD_DEFINITIONS()添加。    |

## 函数

- **PROJECT(hello_cmake)**：

  该命令表示项目的名称是 hello_cmake。
  CMake构建包含一个项目名称，上面的命令会自动生成一些变量，在使用多个项目时引用某些变量会更加容易。比如生成了： PROJECT_NAME 这个变量。
  PROJECT_NAME是变量名，${PROJECT_NAME}是变量值，值为hello_cmake

- **CMAKE_MINIMUM_REQUIRED(VERSION 2.6)** ：

  限定了 CMake 的版本。

- **AUX_SOURCE_DIRECTORY(< dir > < variable >)**：

  `AUX_SOURCE_DIRECTORY ( . DIR_SRCS)`：将当前目录中的源文件名称赋值给变量 DIR_SRCS

- **ADD_SUBDIRECTORY(src)**： 

  指明本项目包含一个子目录 src

- **SET(SOURCES src/Hello.cpp src/main.cpp)**：

  创建一个变量，名字叫SOURCE。它包含了这些cpp文件。

- **ADD_EXECUTABLE(main ${SOURCES })**：

  指示变量 SOURCES 中的源文件需要编译 成一个名称为 main 的**可执行文件**。 ADD_EXECUTABLE() 函数的第一个参数是可执行文件名，第二个参数是要编译的源文件列表。因为这里定义了SOURCE变量，所以就不需要罗列cpp文件了。等价于命令：`ADD_EXECUTABLE(main src/Hello.cpp src/main.cpp)`

- **ADD_LIBRARY(hello_library STATIC src/Hello.cpp)**：

  用于从某些源文件创建一个库，默认生成在构建文件夹。在add_library调用中包含了源文件，用于创建名称为libhello_library.a的静态库。

- **TARGET_LINK_LIBRARIES( main Test )**：

  指明可执行文件 main 需要连接一个名为Test的链接库。添加链接库。

- **TARGET_INCLUDE_DIRECTORIES(hello_library PUBLIC ${PROJECT_SOURCE_DIR}/include)**：

  添加了一个目录，这个目录是库所包含的头文件的目录，并设置库属性为[PUBLIC](https://github.com/SFUMECJF/cmake-examples-Chinese/blob/main/01-basic/1.3  Static Library.md)。

- **MESSAGE(STATUS “Using bundled Findlibdb.cmake…”)**：

  命令 MESSAGE 会将参数的内容输出到终端。

- **FIND_PATH ()** ：

  指明头文件查找的路径，原型如下：`find_path(< VAR > name1 [path1 path2 ...])` 该命令在参数 path* 指示的目录中查找文件 name1 并将查找到的路径保存在变量 VAR 中。

- **FIND_LIBRARY()**： 

  同 FIND_PATH 类似,用于查找链接库并将结果保存在变量中。

- **find_package**

语法 : `find_package(<PackageName> [version] [EXACT] [QUIET] [MODULE] [REQUIRED] [[COMPONENTS] [components...]] [OPTIONAL_COMPONENTS components...] [NO_POLICY_SCOPE])`

查找并从外部项目加载设置。 `<PackageName>_FOUND` 将设置为指示是否找到该软件包。 找到软件包后，将通过软件包本身记录的变量和“导入的目标”提供特定于软件包的信息。 该`QUIET`选项禁用信息性消息，包括那些如果未找到则表示无法找到软件包的消息`REQUIRED``。REQUIRED`如果找不到软件包，该选项将停止处理并显示一条错误消息。

`COMPONENTS`选件后（或`REQUIRED`选件后，如果有的话）可能会列出所需组件的特定于包装的列表 。后面可能会列出其他可选组件`OPTIONAL_COMPONENTS`。可用组件及其对是否认为找到包的影响由目标包定义。

- **include_directories**

语法 : `include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])`

将给定目录添加到编译器用来搜索包含文件的目录中。相对路径被解释为相对于当前源目录。

包含目录添加到 `INCLUDE_DIRECTORIES` 当前`CMakeLists`文件的目录属性。它们也被添加到`INCLUDE_DIRECTORIES`当前`CMakeLists`文件中每个目标的target属性。目标属性值是生成器使用的属性值。

# 单目录下的`CMakeLists.txt`编写

对于一个简单的工程，可以编写如下的`CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.0.0)
project(demo)
SET(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g")
aux_source_directory(. SRC_LIST)
add_executable(demo ${SRC_LIST})
```

## cmake_minimum_required

指定最低的cmake版本

## project(demo)

表明该项目的名称是“demo”

## aux_source_directory(. SRC_LIST)

将一个目录下的所有源代码放在变量`SRC_LIST`中，Cmake中有许多变量，之后会列举出来

## add_executable(demo ${SRC_LIST})

指示变量中的源文件需要编译成名为“demo”的可执行文件，这里的变量不一定是`SRC_LIST`，只是因为前一条命令将源代码文件存储进了该变量

如果不想将工作目录里的文件都编译，可以不写`aux_source_directory(. SRC_LIST)`这条命令，而是直接罗列出你要编译的文件，如

```cmake
add_executable(demo 路径/xxx.cpp 路径/xxx.cpp})
```

## SET(CMAKE_BUILD_TYPE "Debug")和set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g")

set函数的功能是给变量赋值，`CMAKE_BUILD_TYPE`和`CMAKE_CXX_FLAGS`都是Cmake自有的编译选项，这里将其设为调试模式，主要目的是可以使用vscode的调试功能，并不是必须项

## 补充

可以用set函数将要编译的文件存在某个变量中，再用`add_executable`，如

```cmake
SET (SRC_LST main.c other.c)
add_executable(demo ${SRC_LIST})
```



对于源代码都在一个文件夹下，并且不需要第三方库的项目，这样的`CMakeLists.txt`就够用了，之后依次输入命令

```bash
mkdir build
cd build
cmake -G "Unix Makefiles" .. 
make
```

# 多目录多文件

当源文件太多时，有时需要将某一个功能的源代码文件放到同一个子文件夹中，如

```
./test
|
+--- xxx.cc
|
+--- dir/
|
+---- xxx.cc
|
+---- xxx.h
```

这种情况下，在根目录和子目录下都应各编写一个`CMakeLists.txt`，子目录中的作用是将其编译为静态库由主函数调用

首先编写子目录中的`CMakeLists.txt`

```cmake
# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_LIB_SRCS 变量
aux_source_directory(. DIR_LIB_SRCS)
# 生成链接库
add_library (lib ${DIR_LIB_SRCS})
```

根目录中

```cmake
# CMake 最低版本号要求
cmake_minimum_required (VERSION 3.0)
# 项目信息
project (Demo)
# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)
# 添加 dir 子目录
add_subdirectory(dir)
# 指定生成目标
add_executable(Demo main.cc)
# 添加链接库
target_link_libraries(Demo lib)
```

**注意**：cpp文件中引用头文件依然需要写明相对路径
