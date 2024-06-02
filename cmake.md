# Windows

## 下载和配置MinGW-w64

1.下载MSYS2, 链接：https://www.msys2.org/#installation

2.打开MSYS2 terminal，输入一下指令

```shell
pacman -S --needed base-devel mingw-w64-ucrt-x86_64-toolchain
#1.enter 选择全部
#2.y 开始下载
```

3.配置系统环境变量`C:\msys64\ucrt64\bin`

4.验证，打开git bash：`gcc --version`

## 下载CMake

https://cmake.org/download/



## Task.json/Launch.json

launch.json:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name" : "build & debug",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceRoot}/build/main.exe",   /*需要调试的可执行的绝对路径*/
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "build",   /*在进行launch之前需要进行的工作，跳转到tasks.json，也就是tasks.json中需要执行的任务*/
            "miDebuggerPath": "E:/msys64/mingw64/bin/gdb.exe"
        }
    ]
}
```

tasks.json:

```json
{
    "version": "2.0.0",
    "command": "sh",
    "args": [ "-c" ],
    "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": true,
        "panel": "new"
    },
    "tasks": [
        {
            "label": "cmake",
            "type": "shell",
            "options": {
                "cwd": "${workspaceRoot}/build"
            },
            "args": [ "cmake ${workspaceRoot} .. -G \"MinGW Makefiles\" "]
        },
        {
            "label": "make",
            "type": "shell",
            "args": [ "make -j 8" ],
            "options": {
                "cwd": "${workspaceRoot}/build"
            },
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "build",           /*执行的总体任务，也就是名为Build的任务*/
            "dependsOrder": "sequence", /*按列出的顺序执行任务依赖项*/
            "dependsOn":[               /*执行总体任务的依赖为cmake和make*/
                "cmake",
                "make"
            ]
        },
        {
            "label": "run",
            "type": "shell",
            "options": {
                "cwd": "${workspaceRoot}/build"
            },
            "args": [ "./main/main.exe" ],
        },
        {
            "label": "echo", /*测试预定义变量*/
            "type": "shell",
            "command": "echo ${userHome}"
        }
    ]
}
```

==troubleshoot：==

windows下cmake默认使用的msvc编译器，因此在运行cmake指令时需要加上参数：

```shell
cmake .. -G "MinGW Makefiles"
```

vscode终端默认设置为git bash:

```json
"terminal.integrated.defaultProfile.windows": "Git Bash"
```

make的后缀：

```shell
make -j 8 #并行执行8行，请参考《GNU make》5.4
```

​					参考链接：https://www.gnu.org/software/make/manual/make.html#Parallel-Disable

## 生成库文件

+ 为什么编译时需要生成库文件

  >有时候我们会有多个可执行文件，他们之间用到的某些功能是相同的，我们想把这些共用的功能做成一个库，方便大家一起共享
  >
  >库中的函数可以被可执行文件调用，也可以被其他库文件调用
  >
  >- 库文件又分为**静态库文件**和**动态库文件**：
  >
  >1. 其中静态库相当于直接把代码插入到生成的可执行文件中，会导致体积变大，同样的对库文件进行编译，但生成的可执行文件，不依赖库文件即可运行
  >2. 而动态库则只在生成的可执行文件中生成“插桩”函数（汇编语言中的jump，指定跳转的位置），当可执行文件被加载时会读取指定目录中的.dll文件，加载到内存中空闲的位置，并且替换相应的“插桩”指向的地址为加载后的地址，这个过程称为**重定向**，这样以后函数被调用就会跳转到动态加载的地址去

  ![image-20230317153348367](https://developer.qcloudimg.com/http-save/yehe-9645905/c7e70c51f600957742f3f6e7b7bc19ef.png)

+ 动态链接库是如何别调用的

  参考：https://www.bilibili.com/video/BV1vB4y1V7gR/?spm_id_from=333.999.0.0&vd_sourc

+ window和linux的静态库和动态库后缀

  window:静态库.lib 动态库.dll

  linux:静态库.a 动态库.so

+ cmake如何生成库

  ```cmake
  add_library(libhello STATIC hello.c)#静态链接库
  add_library(libhello SHARED hello.c)#动态链接库
  ```

  

+ 修改生成库文件名字

  ```cmake
  #目标名字对于整个工程来说是唯一的，因此生成库文件时名字前加上lib是为了防止编译时出现相同的目标名
  #上述指令生成的库位liblibhello.a
  set_target_properties(libhello PROPERTIES OUTPUT_NAME "hello") #重新设置静态链接库的名字
  ```

## 多文件编译

```cmake
cmake_minimum_required(VERSION 3.5)
project(HELLOC)
set(CMAKE_EXE_LINKER_FLAGS "-Wl,-Map=output.map")

add_library(libhello STATIC hello.c)    #生成静态库
set_target_properties(libhello PROPERTIES OUTPUT_NAME "hello") #重新设置静态链接库的名字
add_executable(main main.c)
target_link_libraries(main libhello)    #静态库链接到exe文件
```

## 多文件夹编译

E:.
│  CMakeLists.txt
│  
├─build
├─hello
│      CMakeLists.txt
│      hello.c
│      hello.h
│      
└─main
        CMakeLists.txt
        main.c

windows下使用tree指令：

```bat
#一定要打开cmd或者powershell
tree /f
tree /f > tree.txt
```

./main/CMakeLists.txt:

```cmake
message("${PROJECT_SOURCE_DIR}/hello")
include_directories(${PROJECT_SOURCE_DIR}/hello)
add_executable(main main.c)
target_link_libraries(main libhello)    #静态库链接到exe文件
```

./hello/CMakeLists.txt:

```cmake
add_library(libhello STATIC hello.c)    #生成静态库
set_target_properties(libhello PROPERTIES OUTPUT_NAME "hello") #重新设置静态链接库的名字
```

./CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.5)
project(HELLOC)
set(CMAKE_EXE_LINKER_FLAGS "-Wl,-Map=output.map")

add_subdirectory(main)
add_subdirectory(hello)
```



## 调试

![Preprocessor](cmake.assets/Preprocessor.png)

.i文件：预处理之后的文件，用于查看宏替换是否正确

.s文件：汇编文件

.map文件：函数编译之后的地址



# Linux

## VsCode

ref: https://code.visualstudio.com/docs/cpp/cmake-linux

### Cmake Tools on linux

#### Install Cmake

Use cmake version 3.15 or greater. To install cmake, or to get a later version, see the instructions [Kitware APT Repository](https://apt.kitware.com/) Then run the `sh kitware-archive.sh` and `sudo apt-get install cmake`.

#### Install GCC

#### Cmake quick start

```sh
mkdir cmakeQuickStart
cd cmakeQuickStart
code . #open VS code in the current working folder, which becomes our workspace
```

Command Palette->Cmake: Quick Start,创建一个Cmake project.

Command Palette->Cmake: select a kit

**Configure helloworld:** 

select a variant(debug, release, minrelsize, relwithdebinfo): Command palette->Cmake: select a variant.

cmake configure: 根据之前的kit和variant生成一个.build文件。

**Build helloworld:**

select the Build button from the Status bar.

**Debug helloworld:**

Command Palette->cmake: debug.

总结：vscode使用cmake tools的整体流程，create->configure->build->debug/run.

#### Cmake advanced usage:o:

launch.json：

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++ - Build and debug active file",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/bin/main",//需要调试的可执行的绝对路径
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "Build",//在进行launch之前需要进行的工作，跳转到tasks.json，也就是tasks.json中需要执行的任务
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```

tasks.json:

```json
{   
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build"//首先进入到这个文件夹下
    },
    "tasks": [
        {
            "type": "shell",
            "label": "cmake",//第一个任务的标签
            "command": "cmake",//执行的第一个任务cmake ..
            "args": [          //参数..
                ".."
            ]
        },
        {
            "label": "make",//第二个任务的标签
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "make",//执行的第二个任务make
            "args": [

            ]
        },
        {
            "label": "Build",//执行的总体任务，也就是名为Build的任务
			"dependsOrder": "sequence", // 按列出的顺序执行任务依赖项
            "dependsOn":[//执行总体任务的依赖为cmake和make
                "cmake",
                "make"
            ]
        }
    ]

}
```

# CMake

增加编译选项：

```cmake
#方法一：
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -save-temps=obj")#保存编译过程中的预编译.i文件
#方法二：
target_compile_options(<target> [BEFORE]
    <INTERFACE|PUBLIC|PRIVATE> -save-temps=obj
)#<target> must been created by add_executable() or add_library()
```

增加预处理宏定义：

```cmake
#方法一：
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DSTATIC=static")
#方法二：
target_compile_definitions(<target>
    <INTERFACE|PUBLIC|PRIVATE> STATIC=static
) #<target> must been created by add_executable() or add_library()
```

## 参考手册

[Using the GNU Compiler Collection (GCC)](https://gcc.gnu.org/onlinedocs/gcc-12.1.0/gcc/)

[CMake Reference Documentation](https://cmake.org/cmake/help/latest/index.html)
