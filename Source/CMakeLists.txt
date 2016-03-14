cmake_minimum_required(VERSION 3.4)

project(hello)

add_executable(helloworld hello.cxx)

set(INSTALL_PATH bin CACHE STRING "Relative install path")

# CPACK_INSTALL_CMAKE_PROJECTS 	List of four values: Build directory, Project Name, Project Component, Directory in the package 
# https://cmake.org/Wiki/CMake:CPackConfiguration
install(TARGETS helloworld EXPORT helloworld_exp RUNTIME DESTINATION ${INSTALL_PATH} COMPONENT Executables)

install(EXPORT helloworld_exp DESTINATION lib/helloworld)

