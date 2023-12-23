# Windows

## VsCode

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
            "args": [ "./${workspaceFolderBasename}/build/main.exe" ],
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

## 多文件编译



## 多文件夹编译







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
