cmake_minimum_required(VERSION 3.12)
project(PowerDragableTabWidget)

# 设置CMake变量
set(CMAKE_INCLUDE_CURRENT_DIR ON) # 包含当前目录的头文件
set(CMAKE_CXX_STANDARD 11) # 根据.pro文件设置C++11标准
set(CMAKE_CXX_STANDARD_REQUIRED ON) # 要求使用指定的C++标准
set(CMAKE_EXPORT_COMPILE_COMMANDS ON) # 确保生成compile_commands.json

# 查找Qt包
find_package(QT NAMES Qt6 Qt5 COMPONENTS Core Gui Widgets REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Core Gui Widgets REQUIRED)
message(STATUS "Qt版本: ${QT_VERSION_MAJOR}")

# 编译选项
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# 定义源文件列表
set(SOURCES
  dragable_tab_window/dragabletabarea.cpp
  dragable_tab_window/dragabletabbar.cpp
  dragable_tab_window/dragabletabgroup.cpp
  main.cpp
  mainwindow.cpp
)

# 定义头文件列表
set(HEADERS
  dragable_tab_window/dragabletabarea.h
  dragable_tab_window/dragabletabbar.h
  dragable_tab_window/dragabletabgroup.h
  mainwindow.h
)

# 定义UI文件列表
set(FORMS
  mainwindow.ui
)

# 生成UI头文件
qt_wrap_ui(UI_HEADERS ${FORMS})

# 生成MOC文件
qt_wrap_cpp(MOC_HEADERS ${HEADERS})

# 添加包含目录
include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/dragable_tab_window
  ${CMAKE_CURRENT_BINARY_DIR} # 包含自动生成的UI头文件目录
)

# 构建可执行文件
add_executable(${PROJECT_NAME}
  ${SOURCES}
  ${HEADERS}
  ${UI_HEADERS}
  ${MOC_HEADERS}
)

# 链接Qt相关库
target_link_libraries(${PROJECT_NAME}
  Qt${QT_VERSION_MAJOR}::Core
  Qt${QT_VERSION_MAJOR}::Gui
  Qt${QT_VERSION_MAJOR}::Widgets
)

# 安装可执行文件
install(TARGETS ${PROJECT_NAME} DESTINATION bin)

# 构建完成后，添加自定义命令复制compile_commands.json到项目根目录
add_custom_command(
  TARGET ${PROJECT_NAME} POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy_if_different
  ${CMAKE_BINARY_DIR}/compile_commands.json
  ${CMAKE_SOURCE_DIR}/compile_commands.json
  COMMENT "复制compile_commands.json到项目根目录"
)

# 定义安装目标路径（根据.pro文件中的设置）
if(UNIX AND NOT APPLE AND NOT ANDROID)
  set(INSTALL_PATH "/opt/${PROJECT_NAME}/bin")
elseif(QNX)
  set(INSTALL_PATH "/tmp/${PROJECT_NAME}/bin")
else()
  set(INSTALL_PATH "/bin")
endif()

# 更新安装目标路径
install(TARGETS ${PROJECT_NAME} DESTINATION ${INSTALL_PATH})
