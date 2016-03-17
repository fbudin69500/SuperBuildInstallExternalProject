cmake_minimum_required(VERSION 3.4)

include(ExternalProject)

project(My_superbuild)  

message(WARNING "Packaging does not work. Install works. Packaging will seem to work if CMAKE_INSTALL_PREFIX is set to a location that is writable, but package will be empty.")

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
    INSTALL_COMMAND ""
  )

    
if(WIN32)
  set(exeextension ".exe")
endif()
add_custom_command(
  OUTPUT ${CMAKE_INSTALL_PREFIX}/bin/helloworld${exeextension}
  COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_BINARY_DIR}/${dependProjBuild}/${CMAKE_CFG_INTDIR}/helloworld${exeextension} 
          ${CMAKE_INSTALL_PREFIX}/bin/helloworld${exeextension}
  DEPENDS hello 
  )
add_custom_target(my_install DEPENDS ${CMAKE_INSTALL_PREFIX}/bin/helloworld${exeextension} )

install(CODE "execute_process(COMMAND \"${CMAKE_COMMAND}\" --build ${CMAKE_CURRENT_BINARY_DIR} --target my_install)")

set(CPACK_GENERATOR "TGZ")
include(CPack)

