cmake_minimum_required(VERSION 3.11.4)

project(linglong-store)

# 保证项目兼容性
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(VERSION "1.0")
set(QT_VERSION_MAJOR CACHE STRING "The major version of qt.")

set(LINYAPS_INSTALLER_HARDENING
    ON
    CACHE BOOL "enable hardening")

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# configure debug mode and asan
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE
      "Debug"
      CACHE STRING "Choose Release or Debug" FORCE)
endif()

if(LINYAPS_INSTALLER_HARDENING)
  message(STATUS "applying harden settings")
  set(CMAKE_EXE_LINKER_FLAGS
      "${CMAKE_EXE_LINKER_FLAGS} -pie -Wl,-z,relro -Wl,-z,now")
  add_compile_options(-fstack-protector-strong)

  if(NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    message(STATUS "Non-Debug build, enabling FORTIFY_SOURCE")
    add_compile_definitions(_FORTIFY_SOURCE=2)
  endif()
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
  if(NOT CMAKE_DEBUG_ASAN)
    set(CMAKE_CXX_FLAGS_RELEASE "")
    set(CMAKE_CXX_FLAGS_DEBUG "-O0  -Wall -g -ggdb3")
    set(CMAKE_CXX_FLAGS "-O0 -Wall -g -ggdb3")
  else()
    set(CMAKE_CXX_FLAGS_RELEASE "")
    set(CMAKE_CXX_FLAGS_DEBUG
        "-O0 -fsanitize=undefined,address -Wall -g -ggdb3")
    set(CMAKE_CXX_FLAGS "-O0 -fsanitize=undefined,address -Wall -g -ggdb3")
  endif()
endif()

include(GNUInstallDirs)
include_directories(${linglong-installer_SOURCE_DIR}/ll-installer)

add_subdirectory(ll-installer)
