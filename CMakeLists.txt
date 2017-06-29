cmake_minimum_required(VERSION 3.5)
project(myproject)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/CMake")

 # Set a default build type if none was specified
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
   message(STATUS "Setting build type to 'Release' as none was specified.")
   set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)
   # Set the possible values of build type for cmake-gui
   set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release"
     "MinSizeRel" "RelWithDebInfo")
endif()

SET(MYPROJECT_LIB_DIR ${PROJECT_SOURCE_DIR}/libs)
SET(MYPROJECT_SRC_DIR ${PROJECT_SOURCE_DIR}/src)

set(CMAKE_CXX_STANDARD 14)
include(CMake/DownloadProject.cmake)

## Boost DI
set(DI_INCLUDE_DIR ${MYPROJECT_LIB_DIR}/boost.di)
file(DOWNLOAD 
        "https://raw.githubusercontent.com/boost-experimental/di/cpp14/include/boost/di.hpp"
        "${DI_INCLUDE_DIR}/di.hpp"
        SHOW_PROGRESS 
        EXPECTED_MD5;ebb3bbac5912578d26d95bb552bddcd5
        STATUS status 
        LOG log)

add_library(BoostDI INTERFACE)
target_include_directories(BoostDI INTERFACE ${DI_INCLUDE_DIR})

set(Boost_USE_STATIC_LIBS ON)
FIND_PACKAGE(Boost REQUIRED COMPONENTS random system thread filesystem chrono atomic date_time regex)

set(MYPROJECT_DEPS 
  Boost::boost
  Boost::random
  Boost::system
  Boost::thread
  Boost::filesystem
  Boost::chrono
  Boost::atomic
  Boost::date_time
  Boost::regex
  BoostDI)

add_subdirectory(src)

if (WITH_TESTS)
    include (CTest)

    find_program(MEMORYCHECK_COMMAND valgrind)
    if(MEMORYCHECK_COMMAND-NOTFOUND)
      message("[W] Valgrind not found")
    else()
      set(MEMORYCHECK_COMMAND_OPTIONS "--leak-check=full --error-exitcode=1")
    endif()

    add_subdirectory(tests)
    enable_testing()

endif()
