cmake_minimum_required(VERSION 3.16)

project(h5markers LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(h5markers
    src/h5markers.cpp
)

target_compile_options(h5markers PRIVATE
    -Wall
    -Wextra
    -Wpedantic
)

find_package(Threads REQUIRED)
target_link_libraries(h5markers PRIVATE Threads::Threads)
