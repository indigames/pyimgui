cmake_minimum_required(VERSION 2.8.12)

set(TARGET_NAME imgui_python)
project(${TARGET_NAME})

# [IGE]: tweaks for platforms
if(DEFINED ANDROID_PLATFORM)
    set (ANDROID 1)
    if(ANDROID_ABI STREQUAL "armeabi-v7a")
        add_compile_options(-marm -march=armv7-a -mtune=cortex-a15 -mfpu=neon-fp16 -mfloat-abi=softfp)
    elseif(ANDROID_ABI STREQUAL "x86")
        add_compile_options(-march=atom -m32 -msse -msse2 -msse3 -mssse3 -mfpmath=sse -mstackrealign)
    elseif(ANDROID_ABI STREQUAL "arm64-v8a")
        add_compile_options(-march=armv8-a+simd+sve -mtune=cortex-a53 -D__ARM_NEON -D__ARM_NEON__)
    elseif(ANDROID_ABI STREQUAL "x86_64")
        add_compile_options(-march=atom -m64 -msse -msse2 -msse3 -mssse3 -msse4.1 -msse4.2 -mfpmath=sse)
    endif()
    if(CMAKE_BUILD_TYPE STREQUAL "Release")
        add_compile_options(-Ofast)
    else(CMAKE_BUILD_TYPE STREQUAL "Debug")
        add_compile_options(-Og)
    endif()
elseif(MSVC)
    add_compile_options(/MP)
endif()
# [/IGE]

# [IGE]: define python path
if (NOT DEFINED IGE_ROOT)
    set(IGE_ROOT ${CMAKE_CURRENT_SOURCE_DIR})
endif()

set (PYTHON_INCLUDE_DIRS "${IGE_ROOT}/igeLibs/Python/Include")
if (MSVC)
    list(APPEND PYTHON_INCLUDE_DIRS "${IGE_ROOT}/igeLibs/Python/PC")
elseif (OSX)
    list(APPEND PYTHON_INCLUDE_DIRS "${IGE_ROOT}/igeLibs/Python/Mac")
elseif (ANDROID)
    list(APPEND PYTHON_INCLUDE_DIRS "${IGE_ROOT}/igeLibs/Python/Android")
elseif (IOS)
    list(APPEND PYTHON_INCLUDE_DIRS "${IGE_ROOT}/igeLibs/Python/IOS")
endif ()
# [/IGE]

add_library(${TARGET_NAME} STATIC 
    ${CMAKE_CURRENT_SOURCE_DIR}/config-cpp/py_imconfig.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui/core.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui-cpp/imgui.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui-cpp/imgui_demo.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui-cpp/imgui_draw.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui-cpp/imgui_widgets.cpp
)

target_include_directories(${TARGET_NAME} PRIVATE 
    config-cpp
    imgui
    imgui-cpp
    ${PYTHON_INCLUDE_DIRS}
)
