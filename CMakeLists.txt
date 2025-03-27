
cmake_minimum_required(VERSION 3.21...3.30)
# We use the GoogleTest module if it is available (only in CMake 3.9+)
# It requires CMP0054 and CMP0057 to be enabled.
if(POLICY CMP0054)
  cmake_policy(SET CMP0054 NEW)
endif()

if(POLICY CMP0057)
  cmake_policy(SET CMP0057 NEW)
endif()

# CMP0075 Include file check macros honor CMAKE_REQUIRED_LIBRARIES
if(POLICY CMP0075)
  cmake_policy(SET CMP0075 NEW)
endif()
PROJECT(mockcpp LANGUAGES CXX)
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)
# Enable C++ language
enable_language(CXX)
INCLUDE(ProjectVar.txt)
SET(MOCKCPP_XUNIT "gtest" CACHE STRING "Set to gtest/cpputest/cppunit to use other testframeworks.")
set(INCLUDE_INSTALL_DIR include CACHE STRING
    "The subdirectory where header files should be installed")
set(LIB_INSTALL_DIR lib CACHE STRING
    "The subdirectory where libraries should be installed")
set(BIN_INSTALL_DIR bin CACHE STRING
    "The subdirectory where binaries should be installed")
set(CMAKE_INSTALL_DIR share/mockcpp CACHE STRING
    "The subdirectory where CMake package config files should be installed")

ADD_SUBDIRECTORY(src)

install(TARGETS mockcpp
    EXPORT mockcpp
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    INCLUDES DESTINATION include
)

# # Add include directories
# target_include_directories(mockcpp PUBLIC
#     $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>   # for headers when building
#     $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>  # for client in install mode
# )
# # Install the library and its headers
# install(TARGETS my_sample_lib
#         EXPORT mockcppTargets
#         ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
#         LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
#         RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}")

# Install COPYING and LICENSE files to CMAKE_INSTALL_DATAROOTDIR
install(FILES ${CMAKE_SOURCE_DIR}/COPYING ${CMAKE_SOURCE_DIR}/LICENSE
        DESTINATION share/mockcpp)

# IF(MSVC)
# INSTALL(
#    FILES ${CMAKE_BINARY_DIR}/src/Debug/mockcpp.pdb
#    DESTINATION lib)
# ENDIF(MSVC)



#message CMAKE_BUILD_TYPE
install(EXPORT mockcpp
    FILE mockcpp-targets.cmake
    NAMESPACE mockcpp::
    DESTINATION ${CMAKE_INSTALL_DIR}
)
# 配置文件
configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/mockcpp-config.cmake.in
    mockcpp-config.cmake
    INSTALL_DESTINATION "${CMAKE_INSTALL_DIR}"
PATH_VARS
    INCLUDE_INSTALL_DIR
    CMAKE_INSTALL_DIR
)

# includes
set(CMAKE_MODULE_PATH
  "${CMAKE_CURRENT_SOURCE_DIR}"
  ${CMAKE_MODULE_PATH})

# Create a version file
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/mockcpp-config-version.cmake"
    VERSION ${VERSION}
    COMPATIBILITY AnyNewerVersion

)
# 安装配置文件
install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/mockcpp-config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/mockcpp-config-version.cmake"
    DESTINATION share/mockcpp
)
INSTALL(DIRECTORY include/mockcpp DESTINATION include
        PATTERN ".svn" EXCLUDE)

IF(MOCKCPP_INSTALL_BOOST)
  IF(MSVC)
    INSTALL(DIRECTORY 3rdparty/boost DESTINATION 3rdparty
            PATTERN ".svn" EXCLUDE)
  ELSE(MSVC)
    INSTALL(DIRECTORY 3rdparty/boost DESTINATION include
            PATTERN ".svn" EXCLUDE)
  ENDIF()
ENDIF()

# # Call package_project after defining the target
# include(${CMAKE_CURRENT_SOURCE_DIR}/build/gcc-debug/_deps/_project_options-src/src/PackageProject.cmake)
# package_project(
#     NAME mockcpp
#     VERSION ${PROJECT_VERSION}
#     BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR}
#     SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}
#     INSTALL_DIR ${CMAKE_INSTALL_PREFIX}
# )
