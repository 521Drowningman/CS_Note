# Cmake

CMake是一个构建系统生成器，可以使用单个CMakeLists.txt为不同平台上的不同工具集配置项目。

在使用CMake时,我们需要向CMake提供源文件和项目配置描述.

项目配置描述即**CMakeLists.txt**文件,我们把CMake指令放在这个文件中。

**NOTE**:*文件的名称区分大小写，必须命名为`CMakeLists.txt`，CMake才能够解析。*

### 具体实施

**NOTE**:*CMake语言不区分大小写，但是参数区分大小写。*

在实施的过程中,当你提供了源文件和CMakeLists.txt后:

创建`build`目录，在`build`目录下来配置项目(创建一个独立在源文件之外的构建目录的目的是:保持源代码目录的整洁,并方便管理不同的构建配置).

这里build目录里的内容是构建器。

#### 实施过程中遇到的问题(重要)

> 由于我的电脑中有vs 2022, 所以有MSVC这套编译器,在make时CMake会默认生成vs 2022的项目文件;
>
> 默认情况下，在GNU/Linux和macOS系统上，CMake使用Unix Makefile生成器。Windows上，Visual Studio是默认的生成器。

解决方法:

```cmake
set(CMAKE_CXX_COMPILER "电脑中g++.exe的地址")
set(CMAKE_C_COMPILER "电脑中gcc.exe的地址")
```

在终端中:

```bash
mkdir build
cd build 
cmake .. -G "MinGW Makefiles"       # 一定要指定编译为MinGW的Makefile 否则就还是vs版本
# 这里的 -G 选项用于切换生成器
cmake --build .                     # 是一个跨平台的、通用的构建命令，它允许 CMake 根据当前环境和配置自动选择合适的构                                     建工具和命令。
在linux中:
make 相当于 cmake --build .
```

