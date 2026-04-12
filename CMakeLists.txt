cmake_minimum_required(VERSION 3.16)

project(marker_scanner LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(marker_scan
    src/main.cpp
)

target_compile_options(marker_scan PRIVATE
    -Wall
    -Wextra
    -Wpedantic
)

find_package(Threads REQUIRED)
target_link_libraries(marker_scan PRIVATE Threads::Threads)
