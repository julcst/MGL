cmake_minimum_required(VERSION 3.18)
project(mgl)

enable_language(OBJCXX)
enable_language(OBJC)
enable_language(CXX)

FILE(GLOB MyCSources MGL/src/*.c)
FILE(GLOB MyMSources MGL/src/*.m)

add_library(${PROJECT_NAME} SHARED ${MyCSources} ${MyMSources})

target_include_directories(${PROJECT_NAME} PUBLIC MGL/include MGL/include/GL)

set_source_files_properties(MyMSources PROPERTIES COMPILE_FLAGS "-x objective-c")

target_compile_options(mgl PRIVATE "-fobjc-arc")

target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)

set(CMAKE_SHARED_LINKER_FLAGS "-lc++ -framework Quartz -framework AppKit -framework Quartzcore -framework IOKit -framework Metal -framework ApplicationServices -framework CoreGraphics -framework Cocoa  -framework Foundation")

find_package(OpenGL REQUIRED)

include(FetchContent)

FetchContent_Declare(
    SPIRV-Headers
    GIT_REPOSITORY https://github.com/KhronosGroup/SPIRV-Headers.git
    GIT_SHALLOW TRUE
    GIT_TAG main
)
FetchContent_MakeAvailable(SPIRV-Headers)

FetchContent_Declare(
    SPIRV-Tools
    GIT_REPOSITORY https://github.com/KhronosGroup/SPIRV-Tools.git
    GIT_SHALLOW TRUE
    GIT_TAG main
)
FetchContent_MakeAvailable(SPIRV-Tools)

set(ENABLE_OPT OFF CACHE BOOL "" FORCE)
set(ENABLE_HLSL OFF CACHE BOOL "" FORCE)
set(ALLOW_EXTERNAL_SPIRV_TOOLS ON CACHE BOOL "" FORCE)
FetchContent_Declare(
    glslang
    # URL https://github.com/KhronosGroup/glslang/archive/14.3.0.tar.gz
    GIT_REPOSITORY https://github.com/KhronosGroup/glslang.git
    GIT_TAG main
    GIT_SHALLOW TRUE
)
set(SPIRV_CROSS_ENABLE_TESTS OFF CACHE BOOL "" FORCE)
FetchContent_Declare(
    SPIRV-Cross
    GIT_REPOSITORY https://github.com/r58Playz/SPIRV-Cross.git
    GIT_TAG uniform-constants
    GIT_SHALLOW TRUE
)
# FetchContent_Declare(
#     SPIRV-Cross
#     GIT_REPOSITORY https://github.com/KhronosGroup/SPIRV-Cross.git
#     GIT_TAG main
#     GIT_SHALLOW TRUE
# )
FetchContent_Declare(
    OpenGL-Registry
    GIT_REPOSITORY https://github.com/KhronosGroup/OpenGL-Registry.git
    GIT_SHALLOW TRUE
    GIT_TAG main
)
FetchContent_Declare(
    ezxml
    GIT_REPOSITORY https://github.com/lxfontes/ezxml.git
    GIT_SHALLOW TRUE
)
FetchContent_MakeAvailable(glslang SPIRV-Cross)

set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL OFF CACHE BOOL "" FORCE)
add_subdirectory(external/glfw)

target_link_libraries(${PROJECT_NAME}
    OpenGL::GL
    glslang::glslang
    glslang::SPIRV
    glslang::glslang-default-resource-limits
    #SPIRV-Tools
    spirv-cross-c
    #SPIRV-Headers
)
add_subdirectory(test_mgl)
