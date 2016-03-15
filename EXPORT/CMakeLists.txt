cmake_minimum_required(VERSION 3.4)

include(ExternalProject)

project(My_superbuild)  

set(projbuild hello-build)

option(BUILD_SUPERBUILD "Superbuild" ON)

if(BUILD_SUPERBUILD)
  ExternalProject_Add(hello
    ${${proj}_EP_ARGS}
    DOWNLOAD_COMMAND ""
    SOURCE_DIR ../Source
    BINARY_DIR ${projbuild}
    CMAKE_ARGS
      -DCMAKE_CXX_COMPILER:FILEPATH=${CMAKE_CXX_COMPILER}
      -DCMAKE_CXX_FLAGS:STRING=${ep_common_cxx_flags}
      -DCMAKE_C_COMPILER:FILEPATH=${CMAKE_C_COMPILER}
      -DCMAKE_C_FLAGS:STRING=${ep_common_c_flags}
      -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
      -DCMAKE_RUNTIME_OUTPUT_DIRECTORY:PATH=${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
      -DCMAKE_LIBRARY_OUTPUT_DIRECTORY:PATH=${CMAKE_LIBRARY_OUTPUT_DIRECTORY}
      -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY:PATH=${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}
      -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_CURRENT_BINARY_DIR}/hello-install
      -DINSTALL_PATH:STRING=bin
    INSTALL_COMMAND ""
    )
  ExternalProject_Add(hello2
    ${${proj}_EP_ARGS}
    DOWNLOAD_COMMAND ""
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    BINARY_DIR hello2-build
    CMAKE_ARGS
      -DHelloImport:PATH=${projbuild}/helloworld_exp.cmake  # First project needs to be build before we include the file
      -DCMAKE_CXX_COMPILER:FILEPATH=${CMAKE_CXX_COMPILER}
      -DCMAKE_CXX_FLAGS:STRING=${ep_common_cxx_flags}
      -DCMAKE_C_COMPILER:FILEPATH=${CMAKE_C_COMPILER}
      -DCMAKE_C_FLAGS:STRING=${ep_common_c_flags}
      -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
      -DCMAKE_RUNTIME_OUTPUT_DIRECTORY:PATH=${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
      -DCMAKE_LIBRARY_OUTPUT_DIRECTORY:PATH=${CMAKE_LIBRARY_OUTPUT_DIRECTORY}
      -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY:PATH=${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}
      -DBUILD_SUPERBUILD:BOOL=OFF
      -DSUPERBUILD_DIR:PATH=${CMAKE_CURRENT_BINARY_DIR}
    INSTALL_COMMAND ""
    DEPENDS hello
    )
  return()
else()
  include(${SUPERBUILD_DIR}/${projbuild}/hello-exports.cmake)  # First project needs to be build before we include the file
  get_target_property(HELLO_LOCATION helloworld LOCATION)
  install(FILES ${HELLO_LOCATION} RUNTIME DESTINATION bin COMPONENT Executables)
endif()
