# 设置 CMake 的最低版本要求
cmake_minimum_required(VERSION 3.21)

# 定义项目名称和所用语言 (C)
project(martingale_simulation C)

# 设置 C 语言的标准
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# 程序需要 OpenMP 的 C 支持。
find_package(OpenMP REQUIRED COMPONENTS C)
add_executable(martingale_simulation main.c)
target_link_libraries(martingale_simulation PRIVATE OpenMP::OpenMP_C)

if (CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(martingale_simulation PRIVATE -O2)
endif()

# Windows/MinGW 下避免运行“单文件编译”，请通过 CMake 目标运行该可执行文件
