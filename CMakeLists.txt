cmake_minimum_required(VERSION 3.15)

# 定义项目名称和使用 C++ 语言
project(ImGui LANGUAGES CXX)

# 创建静态库 target "imgui"
add_library(imgui STATIC
    imconfig.h
    imgui.h
    imgui.cpp
    imgui_draw.cpp
    imgui_internal.h
    imgui_widgets.cpp
    imstb_rectpack.h
    imstb_textedit.h      # 注意：如果文件名有误，请根据实际情况调整
    imstb_truetype.h
    imgui_demo.cpp
    imgui_tables.cpp
)

# 设置 C++ 标准为 C++17（要求编译器支持 C++17）
target_compile_features(imgui PUBLIC cxx_std_17)
set_target_properties(imgui PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS OFF
)

# 针对 MSVC 平台，使用静态运行时库（相当于 staticruntime "on"）
if(MSVC)
    set_target_properties(imgui PROPERTIES
        MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>"
    )
endif()

# 设置输出目录（其中 $<CONFIG> 表示编译配置，如 Debug 或 Release）
set_target_properties(imgui PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>/ImGui"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>/ImGui"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>/ImGui"
    # 设置中间文件输出目录，类似 premake 中的 "bin-int"
    OBJECT_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin-int/$<CONFIG>/ImGui"
)

# 针对 Windows 系统设置：使用最新的系统版本（如果需要可在外部设置 CMAKE_SYSTEM_VERSION）
if(WIN32)
    # 定义预处理宏，等效于 premake 中的 defines { "IMGUI_API=__declspec(dllexport)" }
    target_compile_definitions(imgui PUBLIC "IMGUI_API=__declspec(dllexport)")
endif()

# 关于不同配置的选项：
# - Debug 模式下，CMake 默认会开启调试符号（相当于 symbols "on"）。
# - Release 模式下则会启用优化（相当于 optimize "on"）。
