# SuperBuildInstallExternalProject

## Project

This project presents 8 different approaches to package, with a "main" project, dependencies built as external projects using CMake.

- 2 approaches fail.
- 3 approaches package the dependencies from within the inner build repository of the main project.
- 3 approaches package the dependencies from the outer build repository.

## Rational

The main issue one encounters when trying to package external projects is to specify 
the path to the tools that need to be packaged. One can manually provide the path to the
tools, but may encounter difficulties if the project is built on a system supporting multiple
configurations in the build tree (such as Visual Studio and XCode). Indeed, in this case,
targets are built in subdirectories (`Debug`, `Release`,...) that are not known at configuration time.

## Approaches

### Inner build
- **EXPORT**: At build time, external dependencies can create an _export_ file [1]. This file contains 
the targets (and their properties) of the project that generates it. If this file exists in the
external dependency projects, one can import it and locate the targets. Since this file is only
created at compilation time, it can only be included in an `ExternalProject_Add()` call.
- **CMAKE_CFG_INTDIR_1_inner**: Since `CMAKE_CFG_INTDIR` is only configured at compilation time [2], its value can
be passed to the main project in an _inner build_. The inner build project can use this value to point to the correct
location where the dependency project was built.
- **CPACK_INSTALL_CMAKE_PROJECTS_innerbuild**: This method relies on `CPack` [3] to package the dependency project. It will work
as long as the dependency project is a CMake project with install rules.


### outer build:
- **INSTALL**: After building each dependency, install the result in a known directory. This will get rid of
the extra subdirectory (`Debug`,...) that exists in the build tree. The install path in known and can
be used to package the external project.
- **CPACK_INSTALL_CMAKE_PROJECTS**: This method relies on `CPack` [3]. It allows to package CMake projects.
- **CMAKE_INSTALL_CONFIG_NAME**: This method is based on an answer to a stackoverflow question [4]. At install time,
if `CMAKE_INSTALL_CONFIG_NAME` is not defined, it is replaced by the value of `BUILD_TYPE`.
- **CMAKE_CFG_INTDIR_0** (failing): This approach tries to directly use the CMake variable `CMAKE_CFG_INTDIR` [2].
It fails because this variable is only known at compile time. At configuration time, its value is replaced
by a variable that is understood by your build system (e.g. on Visual Studio 14: `$(Configuration)` ). However,
the system variable does not get replaced in the install rule. This behavior is expected [2].
- **CMAKE_CFG_INTDIR_1** (failing): This approach builds on the previous approach (**CMAKE_CFG_INTDIR_0**) and tries to
work around the fact that the system variable is only replaced at compilation time. Following the example
 available on `CMAKE_CFG_INTDIR` help page [2], a CMake custom command (`add_custom_command()`) is defined. This command is called
 at install time. This works when installing the project, but it does not work when packaging it. Indeed,
 the custom command specifies an install directory based on `CMAKE_INSTALL_PREFIX`. This directory should not
 be used when packaging the project. Packaging will be successful if `CMAKE_INSTALL_PREFIX` is configured to a
 writable location.

## Approaches not explored in this project

- Forcing external project to a known build type.
- Searching in multiple subdirectories in external project build directory, starting from the most likely (e.g. 'Release')

## Results

**CMAKE_CFG_INTDIR_0** and **CMAKE_CFG_INTDIR_1** fail. There might be ways to fix the errors.

## Contributions

Contributions to this project are welcome. Feel free to submit patches for the project if you
know other methods or if you know how to fix the two approaches that fail.

## References

[1] https://cmake.org/cmake/help/v3.0/command/export.html

[2] https://cmake.org/cmake/help/v3.0/variable/CMAKE_CFG_INTDIR.html

[3] https://cmake.org/Wiki/CMake:CPackConfiguration

[4] http://stackoverflow.com/questions/21198030/installfiles-cmake-cfg-intdir-abc-win-dll-destination-bin
