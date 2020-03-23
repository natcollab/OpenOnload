#[===============================================================================[.rst
# OpenOnload/CMakeLists.txt
#
# OpenOnload library.
# ^^^^^^^^^^^^^^^^^^^
#
# Imported targets in global scope:
#
#   OpenOnload::OpenOnload
#
#]===============================================================================]

# Setup minimum required version of cmake, policy
# This command should be the first command in the project
cmake_minimum_required(VERSION 3.13...3.16)

# Setup Project name and version
# CMakeTest_VERSION and PROJECT_VERSION variables are set
project(OpenOnload VERSION 1.0
  DESCRIPTION "Interface to OpenOnload library"
  LANGUAGES CXX
  )

#=================================================================================

# Target: OpenOnload
add_library(OpenOnload INTERFACE)
add_library(OpenOnload::OpenOnload ALIAS OpenOnload)

set(OpenOnload_INCLUDE_INSTALL_DIR include/OpenOnload)
set(OpenOnload_LIB_INSTALL_DIR lib/OpenOnload)
set(OpenOnload_CMAKE_INSTALL_DIR lib/cmake/OpenOnload)

target_include_directories(OpenOnload
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${OpenOnload_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(OpenOnload
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libciul1.a>
             $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libonload_ext.a>
             $<INSTALL_INTERFACE:${OpenOnload_LIB_INSTALL_DIR}/libciul1.a>
             $<INSTALL_INTERFACE:${OpenOnload_LIB_INSTALL_DIR}/libonload_ext.a>
  )

#=================================================================================

# Install : Trader
install(DIRECTORY include/
  DESTINATION ${OpenOnload_INCLUDE_INSTALL_DIR}
  )

install(FILES lib/libciul1.a
              lib/libonload_ext.a
  DESTINATION ${OpenOnload_LIB_INSTALL_DIR}
  )

#=================================================================================

# Install export targets

install(TARGETS OpenOnload EXPORT OpenOnload_export)

# TODO: library path in export target file is relative path, which is not correct
install(EXPORT OpenOnload_export
  NAMESPACE OpenOnload::
  FILE OpenOnloadTargets.cmake
  DESTINATION ${OpenOnload_CMAKE_INSTALL_DIR}
  )

#=================================================================================

# Install package config and version files

include(CMakePackageConfigHelpers)

# Package version file
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/OpenOnloadConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
  )

# Package config file
configure_package_config_file(
  ${CMAKE_CURRENT_LIST_DIR}/cmake/OpenOnloadConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/OpenOnloadConfig.cmake
  INSTALL_DESTINATION ${OpenOnload_CMAKE_INSTALL_DIR}
  PATH_VARS
    OpenOnload_INCLUDE_INSTALL_DIR
    OpenOnload_LIB_INSTALL_DIR
  )

# Install the config and version files
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/OpenOnloadConfig.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/OpenOnloadConfigVersion.cmake
  DESTINATION ${OpenOnload_CMAKE_INSTALL_DIR}
  )
