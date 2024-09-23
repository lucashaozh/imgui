cmake_minimum_required(VERSION 3.10)
project(ImGui)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

file(GLOB IMGUI_MISC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/misc/*.h ${CMAKE_CURRENT_SOURCE_DIR}/misc/*.cpp)
file(GLOB IMGUI_FILES ${CMAKE_CURRENT_SOURCE_DIR}/*.h ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

# 将所有文件添加到 ImGui 库中
add_library(ImGui STATIC
    ${IMGUI_FILES}
    ${IMGUI_MISC_FILES}
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.h
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.h
)

target_link_libraries(ImGui PRIVATE glfw)

target_include_directories(ImGui PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

if (WIN32)
    # Windows specific settings
    set_target_properties(ImGui PROPERTIES
        WINDOWS_EXPORT_ALL_SYMBOLS ON
    )
elseif (UNIX)
    # Linux specific settings
    set_target_properties(ImGui PROPERTIES
        POSITION_INDEPENDENT_CODE ON
    )
endif()

# Configuration specific settings
set_target_properties(ImGui PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/bin/Debug
    RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/bin/Release
    ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/lib/Debug
    ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/lib/Release
)

# Debug configuration
target_compile_definitions(ImGui PRIVATE $<$<CONFIG:Debug>:DEBUG>)
target_compile_options(ImGui PRIVATE $<$<CONFIG:Debug>:-g>)

# Release configuration
target_compile_definitions(ImGui PRIVATE $<$<CONFIG:Release>:NDEBUG>)
target_compile_options(ImGui PRIVATE $<$<CONFIG:Release>:-O3>)
