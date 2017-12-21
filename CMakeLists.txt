cmake_minimum_required(VERSION 3.0)

project(ibl)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_subdirectory(bgfx.cmake)

file(GLOB SRC "./bgfx.cmake/bgfx/examples/00-helloworld/helloworld.cpp")

add_executable(helloworld ${SRC})
add_definitions(-DENTRY_CONFIG_IMPLEMENT_MAIN=1)
target_include_directories(helloworld PUBLIC 
	bx/include
	bgfx/3rdparty
	bgfx/examples
	bgfx/examples/common
	bgfx/examples/00-helloworld)
target_link_libraries(helloworld example-common)
