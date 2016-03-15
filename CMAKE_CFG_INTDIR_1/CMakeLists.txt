cmake_minimum_required(VERSION 3.4)

include(ExternalProject)

project(My_superbuild)  

set(projbuild hello-build)

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

add_custom_command(
  OUTPUT ${CMAKE_INSTALL_PREFIX}/bin/helloworld 
  COMMAND ${CMAKE_COMMAND} -E copy ${projbuild}/${CMAKE_CFG_INTDIR}/helloworld 
          ${CMAKE_INSTALL_PREFIX}/bin/helloworld
  DEPENDS hello 
  )
add_custom_target(my_install DEPENDS ${CMAKE_INSTALL_PREFIX}/bin/helloworld )

install(CODE "execute_process(COMMAND \"${CMAKE_COMMAND}\" --build ${CMAKE_CURRENT_BINARY_DIR} --target my_install)")
