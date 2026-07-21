# 现代 CMake 完全指南

> 本文系统讲解 Modern CMake（CMake 3.0+）的核心思想、常用命令与最佳实践。

---

## 一、什么是现代 CMake

### 1.1 传统 CMake vs 现代 CMake

**传统 CMake（CMake 2.x 风格）**
- 以**目录/变量**为中心：`include_directories()`、`link_directories()`、`add_definitions()`
- 全局污染：所有目录共享同一套头文件路径和编译选项
- 难以维护：依赖关系不清晰，跨目录传递混乱

**现代 CMake（CMake 3.0+ 风格）**
- 以 **Target（目标）** 为中心：所有属性都挂在 target 上
- 命令前缀 `target_*`：`target_include_directories()`、`target_link_libraries()`、`target_compile_options()`
- 自带**传递性（transitivity）**：依赖自动向上传播
- 模块化、可复用：一个 target 就是一个完整的构建单元

### 1.2 核心哲学

> **不要设置全局变量，要操作 Target 的属性。**

每个 target 都有自己的：
- 头文件搜索路径（`INCLUDE_DIRECTORIES`）
- 编译选项（`COMPILE_OPTIONS`）
- 预处理器定义（`COMPILE_DEFINITIONS`）
- 链接库（`LINK_LIBRARIES`）
- C++ 标准（`CXX_STANDARD`）
- ……以及数十种其他属性

属性有三种可见性：
| 关键字 | 含义 |
|--------|------|
| `PRIVATE` | 只对当前 target 自身生效，不传递给依赖者 |
| `PUBLIC` | 对当前 target 生效，并且传递给所有链接它的 target |
| `INTERFACE` | 自身不使用，只传递给链接它的 target（常用于头文件库） |

---

## 二、工程基础结构

### 2.1 最小可用 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyApp VERSION 1.0.0 LANGUAGES CXX)

add_executable(myapp main.cpp)
```

### 2.2 project 命令详解

```cmake
project(<name>
    [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
    [DESCRIPTION <project-description-string>]
    [HOMEPAGE_URL <url-string>]
    [LANGUAGES <language-name>...]
)
```

- `VERSION`：设置项目版本，自动生成 `<PROJECT-NAME>_VERSION_MAJOR` 等变量
- `LANGUAGES`：指定启用的语言，默认 `C CXX`
- `DESCRIPTION`：项目描述字符串

### 2.3 标准目录结构推荐

```
myproject/
├── CMakeLists.txt      # 顶层
├── include/            # 公开头文件
│   └── mylib/
├── src/                # 源文件 + 私有头文件
│   ├── CMakeLists.txt
│   └── ...
├── tests/              # 单元测试
│   └── CMakeLists.txt
├── examples/           # 示例
└── cmake/              # 自定义 .cmake 模块
```

---

## 三、Target 命令详解

### 3.1 创建 Target

#### 可执行文件
```cmake
add_executable(myapp main.cpp utils.cpp)
```

#### 静态库 / 共享库
```cmake
# 静态库 (.a / .lib)
add_library(mylib STATIC src/a.cpp src/b.cpp)

# 共享库 (.so / .dll)
add_library(mylib SHARED src/a.cpp src/b.cpp)

# 对象库（只编译不归档，方便被多个 target 复用）
add_library(mylib OBJECT src/a.cpp src/b.cpp)
```

#### 接口库（Header-only）
```cmake
add_library(myheaderlib INTERFACE)
target_include_directories(myheaderlib INTERFACE include/)
```

### 3.2 头文件路径

```cmake
target_include_directories(mylib
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include   # 对外暴露
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src       # 仅内部使用
)
```

**关键：** 用 `PUBLIC` 意味着任何链接 `mylib` 的 target 都会自动获得这条 include 路径，无需手动再写一遍。

### 3.3 链接库

```cmake
target_link_libraries(myapp
    PRIVATE
        mylib       # 链接自己工程里的 target
        Threads::Threads  # 链接 CMake 提供的导入目标
)
```

传递性示例：
```
A (PUBLIC 链接 B) → 任何链接 A 的 target 自动链接 B
A (PRIVATE 链接 B) → B 只在 A 内部使用，不向外传递
```

### 3.4 编译选项与宏定义

```cmake
# 编译选项
target_compile_options(mylib PRIVATE -Wall -Wextra -Wpedantic)

# 预处理器宏
target_compile_definitions(mylib
    PUBLIC  MYLIB_VERSION=1
    PRIVATE MYLIB_INTERNAL_BUILD
)

# C++ 标准
target_compile_features(mylib PUBLIC cxx_std_17)
# 或者
set_target_properties(mylib PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON
    CXX_EXTENSIONS OFF
)
```

### 3.5 set_target_properties 批量设置属性

```cmake
set_target_properties(mylib PROPERTIES
    VERSION   ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
    OUTPUT_NAME "mylib_special"
    POSITION_INDEPENDENT_CODE ON
)
```

---

## 四、变量与构建类型

### 4.1 常用内置变量

| 变量 | 含义 |
|------|------|
| `CMAKE_SOURCE_DIR` | 顶层 CMakeLists.txt 所在目录 |
| `CMAKE_BINARY_DIR` | 构建根目录（build 目录） |
| `CMAKE_CURRENT_SOURCE_DIR` | 当前正在处理的 CMakeLists.txt 所在目录 |
| `CMAKE_CURRENT_BINARY_DIR` | 当前处理目录对应的构建输出目录 |
| `PROJECT_SOURCE_DIR` | 最近一次 `project()` 命令的源码目录 |
| `PROJECT_BINARY_DIR` | 最近一次 `project()` 命令的构建目录 |
| `CMAKE_BUILD_TYPE` | 构建类型（Debug/Release/RelWithDebInfo/MinSizeRel） |

### 4.2 构建类型

单配置生成器（Makefile、Ninja）：
```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build
```

多配置生成器（Visual Studio、Xcode）：
```bash
cmake -S . -B build
cmake --build build --config Release
```

四种标准构建类型：
- `Debug`：无优化，带调试信息
- `Release`：全速优化，无调试信息
- `RelWithDebInfo`：优化 + 调试信息（接近 Release 性能）
- `MinSizeRel`：最小体积优化

### 4.3 自定义选项 option

```cmake
option(MYLIB_BUILD_TESTS "Build unit tests" ON)
option(MYLIB_BUILD_SHARED "Build as shared library" OFF)

if(MYLIB_BUILD_TESTS)
    add_subdirectory(tests)
endif()
```

命令行覆盖：
```bash
cmake -S . -B build -DMYLIB_BUILD_TESTS=OFF
```

---

## 五、依赖管理

### 5.1 find_package

现代 CMake 推荐使用 **Config-file 模式** 的 `find_package`，它会直接导入 target：

```cmake
find_package(Boost 1.70 REQUIRED COMPONENTS filesystem system)

target_link_libraries(myapp PRIVATE Boost::filesystem Boost::system)
```

常见的导入目标命名空间：
- `Boost::*`
- `Qt5::*` / `Qt6::*`
- `Threads::Threads`
- `OpenSSL::SSL`、`OpenSSL::Crypto`
- `ZLIB::ZLIB`

### 5.2 FetchContent（3.11+）

直接从远程拉取依赖并参与构建，无需系统安装：

```cmake
include(FetchContent)

FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt.git
    GIT_TAG        10.0.0
)
FetchContent_MakeAvailable(fmt)

target_link_libraries(myapp PRIVATE fmt::fmt)
```

### 5.3 pkg_check_modules（兜底方案）

对于没有 CMake config 的库，可以回退到 pkg-config：

```cmake
find_package(PkgConfig REQUIRED)
pkg_check_modules(GTK3 REQUIRED IMPORTED_TARGET gtk+-3.0)

target_link_libraries(myapp PRIVATE PkgConfig::GTK3)
```

---

## 六、安装与导出

### 6.1 install 基础

```cmake
# 安装可执行文件 / 库
install(TARGETS myapp mylib
    EXPORT mylib-targets
    RUNTIME  DESTINATION bin
    LIBRARY  DESTINATION lib
    ARCHIVE  DESTINATION lib
    INCLUDES DESTINATION include
)

# 安装头文件
install(DIRECTORY include/mylib
    DESTINATION include
)
```

### 6.2 导出 Target（让别人能 find_package）

```cmake
# 导出 target 配置
install(EXPORT mylib-targets
    FILE        mylibTargets.cmake
    NAMESPACE   mylib::
    DESTINATION lib/cmake/mylib
)

# 生成版本兼容文件
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    mylibConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

# 安装 Config 文件
install(FILES
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/mylibConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/mylibConfigVersion.cmake"
    DESTINATION lib/cmake/mylib
)
```

安装完成后，其他项目就可以：
```cmake
find_package(mylib REQUIRED)
target_link_libraries(otherapp PRIVATE mylib::mylib)
```

---

## 七、测试集成

### 7.1 CTest + Google Test 示例

```cmake
enable_testing()

find_package(GTest REQUIRED)

add_executable(mylib_tests test_mylib.cpp)
target_link_libraries(mylib_tests
    PRIVATE
        mylib
        GTest::gtest_main
)

include(GoogleTest)
gtest_discover_tests(mylib_tests)
```

运行测试：
```bash
cmake --build build
ctest --test-dir build -V
```

---

## 八、生成器表达式（Generator Expressions）

生成器表达式在**生成构建系统时**求值（不是配置阶段），用于根据配置条件化设置属性。

### 常用表达式

```cmake
# 根据构建类型设置选项
target_compile_options(mylib PRIVATE
    $<$<CONFIG:Debug>:-O0 -g>
    $<$<CONFIG:Release>:-O3 -DNDEBUG>
)

# 按编译器区分选项
target_compile_options(mylib PRIVATE
    $<$<CXX_COMPILER_ID:MSVC>:/W4>
    $<$<CXX_COMPILER_ID:GNU,Clang,AppleClang>:-Wall -Wextra>
)

# 判断是否为 C++
$<COMPILE_LANGUAGE:CXX>

# 条件拼接
$<IF:condition,true_val,false_val>
```

---

## 九、完整实战示例

### 目录结构

```
mymath/
├── CMakeLists.txt
├── include/
│   └── mymath/
│       └── vector.h
├── src/
│   ├── vector.cpp
│   └── CMakeLists.txt
└── tests/
    ├── CMakeLists.txt
    └── test_vector.cpp
```

### 顶层 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)
project(mymath
    VERSION 1.0.0
    DESCRIPTION "A simple math library"
    LANGUAGES CXX
)

option(MYMATH_BUILD_TESTS "Build tests" ON)

# 全局只设最基本的，具体属性交给 target
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_subdirectory(src)

if(MYMATH_BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()
```

### src/CMakeLists.txt

```cmake
add_library(mymath STATIC
    vector.cpp
)

target_include_directories(mymath
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/../include
)

target_compile_features(mymath PUBLIC cxx_std_17)

target_compile_options(mymath PRIVATE
    $<$<CXX_COMPILER_ID:GNU,Clang,AppleClang>:-Wall -Wextra -Wpedantic>
    $<$<CXX_COMPILER_ID:MSVC>:/W4>
)
```

### tests/CMakeLists.txt

```cmake
find_package(GTest REQUIRED)

add_executable(mymath_tests test_vector.cpp)
target_link_libraries(mymath_tests
    PRIVATE
        mymath
        GTest::gtest_main
)

include(GoogleTest)
gtest_discover_tests(mymath_tests)
```

---

## 十、最佳实践清单

### ✅ 推荐

1. **最低版本设 3.15+**：享受 FetchContent、目标属性完善等特性
2. **一切围绕 target**：优先用 `target_*` 命令，远离 `include_directories` 等全局命令
3. **明确 PUBLIC / PRIVATE / INTERFACE**：不要省略可见性关键字
4. **优先使用导入目标**：`Boost::filesystem` 优于 `${Boost_LIBRARIES}`
5. **用 `target_compile_features` 设置标准**：比全局 `CMAKE_CXX_STANDARD` 更精确
6. **`project()` 里写全 VERSION 和 LANGUAGES**
7. **构建外编译（out-of-source build）**：`cmake -S . -B build`

### ❌ 避免

1. `file(GLOB ...)` 收集源文件（新增文件不会自动触发重配置）
2. 滥用 `add_definitions()`、`include_directories()`、`link_directories()`
3. 手动设置 `CMAKE_CXX_FLAGS`（用 `target_compile_options` 替代）
4. 省略可见性关键字（CMake 会根据上下文推断，行为容易混乱）
5. 把第三方依赖的 include 目录直接塞到全局

---

## 十一、常用命令速查表

| 分类 | 现代命令（推荐） | 传统命令（避免） |
|------|------------------|------------------|
| 头文件路径 | `target_include_directories()` | `include_directories()` |
| 链接库 | `target_link_libraries()` | `link_libraries()` |
| 编译选项 | `target_compile_options()` | `add_compile_options()` |
| 宏定义 | `target_compile_definitions()` | `add_definitions()` |
| 源文件 | `target_sources()` | 直接列在 add_* 里 |
| C++ 标准 | `target_compile_features()` | `set(CMAKE_CXX_FLAGS ...)` |

---

## 参考资源

- [CMake 官方文档](https://cmake.org/cmake/help/latest/)
- [Professional CMake（Craig Scott）](https://crascit.com/professional-cmake/)
- [An Introduction to Modern CMake](https://cliutils.gitlab.io/modern-cmake/)
- [It's Time To Do CMake Right](https://pabloariasal.github.io/2018/02/19/its-time-to-do-cmake-right/)
