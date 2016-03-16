cmake_minimum_required(VERSION 3.4)

include(ExternalProject)

project(My_superbuild)  

set(projbuild hello-build)

message(FATAL_ERROR "This method does not work. It is solely to illustrate a method found online.")

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

install(FILES "${projbuild}/\${CMAKE_INSTALL_CONFIG_NAME}/helloworld"
        DESTINATION "Bin")
set(CPACK_GENERATOR "TGZ")
include(CPack)

