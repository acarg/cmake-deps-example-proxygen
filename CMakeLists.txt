cmake_minimum_required(VERSION 3.10.2)

set(CMAKE_CXX_STANDARD 17)

project(cmake-deps-example)

add_definitions("-Wall -Wextra -Werror ")

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules")

find_package(gflags REQUIRED)
find_package(Proxygen REQUIRED)

add_executable(echo_server
    src/EchoServer.cpp
    src/EchoHandler.cpp
    )

target_link_libraries(echo_server
    proxygen::proxygen
    proxygen::proxygenhttpserver
    )
