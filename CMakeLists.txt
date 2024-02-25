cmake_minimum_required(VERSION 3.8)
project(irrlicht_example)

# 设置 Irrlicht 的头文件目录
include_directories(/usr/local/Cellar/irrlicht/1.8.5_1/include)

# 添加项目 include 目录
include_directories(${CMAKE_SOURCE_DIR}/include)

# 添加可执行文件
add_executable(example
  src/main.cpp
)

# 寻找 Irrlicht 库
find_library(IRRLICHT_LIBRARY NAMES Irrlicht PATHS /usr/local/Cellar/irrlicht/1.8.5_1/lib NO_DEFAULT_PATH)

# 链接 Irrlicht 库
target_link_libraries(example
  PRIVATE
    ${IRRLICHT_LIBRARY}
)

# 定义要跳过的示例程序目录的黑名单
set(SKIP_DIRS "examples/14.Win32Window" "examples/21.Quake3Explorer" "examples/Demo")

# 获取所有示例目录
file(GLOB EXAMPLE_DIRS RELATIVE ${CMAKE_SOURCE_DIR} examples/*)

foreach(EXAMPLE_DIR IN LISTS EXAMPLE_DIRS)
    # 检查目录是否在黑名单中
    list(FIND SKIP_DIRS ${EXAMPLE_DIR} INDEX)
    message(STATUS "INDEX: ${INDEX} EXAMPLE_DIR: ${EXAMPLE_DIR}")
    if(INDEX GREATER -1)
        message(STATUS "Skipping ${EXAMPLE_DIR} as it is in the skip list.")
        continue() # 跳过当前循环的剩余部分
    endif()
 
    # 检查目录中是否存在 main.cpp 文件
    if(EXISTS "${CMAKE_SOURCE_DIR}/${EXAMPLE_DIR}/main.cpp")
        # 获取示例的名称，用于创建目标
        get_filename_component(EXAMPLE_NAME ${EXAMPLE_DIR} NAME)

        # 添加可执行文件
        add_executable(${EXAMPLE_NAME} ${EXAMPLE_DIR}/main.cpp)

        # 链接 Irrlicht 库
        target_link_libraries(${EXAMPLE_NAME} ${IRRLICHT_LIBRARY})
    endif()
endforeach()