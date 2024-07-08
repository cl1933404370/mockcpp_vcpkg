
cmake_minimum_required(VERSION 3.21...3.30)

PROJECT(mockcpp)
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

INCLUDE(ProjectVar.txt)

ADD_SUBDIRECTORY(src)

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

SET(MOCKCPP_XUNIT "testngpp" CACHE STRING "Set to gtest/cpputest/cppunit to use other testframeworks.")
