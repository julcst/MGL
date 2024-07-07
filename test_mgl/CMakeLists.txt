cmake_minimum_required(VERSION 3.18)
project(test_mgl)

set(CMAKE_EXE_LINKER_FLAGS "-framework Quartzcore -framework IOKit -framework Cocoa -framework Foundation")

add_executable(test_mgl main.cpp)

target_compile_definitions(test_mgl PUBLIC)

find_package(glm REQUIRED)

target_link_libraries(test_mgl
    mgl
    glfw
)


