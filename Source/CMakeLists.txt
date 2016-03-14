cmake_minimum_required(VERSION 3.4)

project(hello)

add_executable(helloworld hello.cxx)

set(INSTALL_PATH bin CACHE STRING "Relative install path")


