cmake_minimum_required(VERSION 3.6 FATAL_ERROR)

project(leetcode
  LANGUAGES CXX
  VERSION 1.0.0
)

# <<< General set up >>>
# set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
# set(CMAKE_CXX_FLAGS "-Wall -std=c++11 -pthread")
# set(CMAKE_C_FLAGS "-Wall -std=c++11 -pthread")

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()
include(GNUInstallDirs)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_BINDIR})
# Offer the user the choice of overriding the installation directories
set(INSTALL_LIBDIR ${CMAKE_INSTALL_LIBDIR} CACHE PATH "Installation directory for libraries")
set(INSTALL_BINDIR ${CMAKE_INSTALL_BINDIR} CACHE PATH "Installation directory for executables")
set(INSTALL_INCLUDEDIR ${CMAKE_INSTALL_INCLUDEDIR} CACHE PATH "Installation directory for header files")
if(WIN32 AND NOT CYGWIN)
  set(DEF_INSTALL_CMAKEDIR CMake)
else()
#   set(DEF_INSTALL_CMAKEDIR share/cmake/${PROJECT_NAME})
endif()

add_library(leetcode SHARED "")
set(SOURCE_CODE
    util.cpp
)
set(HEADERS
    include/util.h
)
target_sources(leetcode PRIVATE ${SOURCE_CODE})


set_target_properties(leetcode
  PROPERTIES
    POSITION_INDEPENDENT_CODE 1
    # CXX_VISIBILITY_PRESET hidden
    # VISIBILITY_INLINES_HIDDEN 4
    SOVERSION ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    OUTPUT_NAME "leetcode"
    DEBUG_POSTFIX "_d"
    # PUBLIC_HEADER ${_PUBLIC_HEADER}
    MACOSX_RPATH ON
  )

target_include_directories(leetcode PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/include/leetcode>)


# Create and install the CMake configuration files.
include(CMakePackageConfigHelpers)
# configure_file("config.cmake.in" "leetcodeConfig.cmake" @ONLY)
write_basic_package_version_file(
    "leetcodeConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion)

install(TARGETS leetcode EXPORT leetcodeTargets LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib COMPONENT lib
PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_PREFIX}/include/leetcode COMPONENT dev
ARCHIVE DESTINATION ${CMAKE_INSTALL_PREFIX}/lib COMPONENT lib)

install(EXPORT leetcodeTargets DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/leetcode COMPONENT dev)

configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/config.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/leetcodeConfig.cmake
  INSTALL_DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/leetcode
  )


# ref. https://newbedev.com/how-to-configure-cmakelists-txt-to-install-public-headers-of-a-shared-library
install(
  FILES ${HEADERS}
  DESTINATION ${CMAKE_INSTALL_PREFIX}/include/leetcode
)
install(
  FILES
    ${CMAKE_CURRENT_BINARY_DIR}/leetcodeConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/leetcodeConfigVersion.cmake
  DESTINATION
  ${CMAKE_INSTALL_PREFIX}/lib/cmake/leetcode
)