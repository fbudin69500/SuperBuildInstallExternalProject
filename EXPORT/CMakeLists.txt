cmake_minimum_required(VERSION 3.4)

include(ExternalProject)

project(My_superbuild)  

option(BUILD_SUPERBUILD "Superbuild" ON)

if(BUILD_SUPERBUILD)
  set(dependProj dependProj)
  set(dependProjBuild ${dependProj}-build)
  ExternalProject_Add(${dependProj}
    ${${proj}_EP_ARGS}
    DOWNLOAD_COMMAND ""
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../Source
    BINARY_DIR ${dependProjBuild}
    CMAKE_ARGS
      -DCMAKE_CXX_COMPILER:FILEPATH=${CMAKE_CXX_COMPILER}
      -DCMAKE_CXX_FLAGS:STRING=${ep_common_cxx_flags}
      -DCMAKE_C_COMPILER:FILEPATH=${CMAKE_C_COMPILER}
      -DCMAKE_C_FLAGS:STRING=${ep_common_c_flags}
      -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
      -DCMAKE_RUNTIME_OUTPUT_DIRECTORY:PATH=${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
      -DCMAKE_LIBRARY_OUTPUT_DIRECTORY:PATH=${CMAKE_LIBRARY_OUTPUT_DIRECTORY}
      -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY:PATH=${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}
      -DINSTALL_PATH:STRING=bin
    INSTALL_COMMAND ""
    )
  set(mainProj mainProj)
  set(mainProjBuild ${mainProj}-build)
  ExternalProject_Add(${mainProj}
    ${${proj}_EP_ARGS}
    DOWNLOAD_COMMAND ""
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    BINARY_DIR ${mainProjBuild}
    CMAKE_ARGS
      -DHelloImport:PATH=${dependProjBuild}/hello-exports.cmake  # First project needs to be build before we include the file
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
    DEPENDS ${dependProj}
    )
  return()
else()
  include(${SUPERBUILD_DIR}/${HelloImport})  # First project needs to be build before we include the file
  get_target_property(HELLO_LOCATION helloworld LOCATION)
  install(FILES ${HELLO_LOCATION} DESTINATION bin COMPONENT Executables)
  set(CPACK_GENERATOR "TGZ")
  include(CPack)

endif()

