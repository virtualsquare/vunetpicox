cmake_minimum_required(VERSION 3.13)
project("vunetpicox"
    DESCRIPTION "A VUOS vunet module for picoxnet"
    HOMEPAGE_URL "https://github.com/virtualsquare/vunetpicox"
    VERSION 0.1.0
    LANGUAGES C)

# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_FORTIFY_SOURCE=2 -O2 -pedantic -Wall -Wextra")

include(GNUInstallDirs)
include(CheckIncludeFile)
include(CheckSymbolExists)

set(LIBS_REQUIRED picoxnet)
set(HEADERS_REQUIRED picoxnet.h vunet.h nlinline.h strcase.h)
set(CMAKE_REQUIRED_QUIET TRUE)
set(CMAKE_REQUIRED_LIBRARIES slirp)

foreach(THISLIB IN LISTS LIBS_REQUIRED)
  find_library(LIB${THISLIB}_OK ${THISLIB})
  if(NOT LIB${THISLIB}_OK)
    message(FATAL_ERROR "library lib${THISLIB} not found")
  endif()
endforeach(THISLIB)

foreach(HEADER IN LISTS HEADERS_REQUIRED)
  check_include_file(${HEADER} ${HEADER}_OK)
  if(NOT ${HEADER}_OK)
    message(FATAL_ERROR "header file ${HEADER} not found")
  endif()
endforeach(HEADER)

add_definitions(-D_GNU_SOURCE)
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

add_library(vunetpicox SHARED vunetpicox.c)
target_link_libraries(vunetpicox picoxnet)

set_target_properties(vunetpicox PROPERTIES PREFIX "")

install(TARGETS vunetpicox DESTINATION ${CMAKE_INSTALL_LIBDIR}/vu/modules)

add_subdirectory(man)

add_custom_target(uninstall
  "${CMAKE_COMMAND}" -P "${PROJECT_SOURCE_DIR}/Uninstall.cmake")

