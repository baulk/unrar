cmake_minimum_required(VERSION 3.20)

project(unrar)

if(CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR AND NOT MSVC_IDE)
  message(
    FATAL_ERROR
      "In-source builds are not allowed.
CMake would overwrite the makefiles distributed with UNRAR.
Please create a directory and run cmake from there, passing the path
to this source directory as the last argument.
This process created the file `CMakeCache.txt' and the directory `CMakeFiles'.
Please delete them.")
endif()

if(MSVC)
  set(CMAKE_MSVC_RUNTIME_LIBRARY_DEFAULT "")
  # FIXME: This block should go away after a transition period.
  if(NOT CMAKE_VERSION VERSION_LESS 3.15)
    # Filter out MSVC runtime library flags that may have come from the cache of an existing build tree or from scripts.
    foreach(l C CXX)
      foreach(c DEBUG MINSIZEREL RELEASE RELWITHDEBINFO)
        string(REGEX REPLACE "[-/]M[DT]d?( |$)" "" "CMAKE_${l}_FLAGS_${c}" "${CMAKE_${l}_FLAGS_${c}}")
      endforeach()
    endforeach()
  endif()
  # http://stackoverflow.com/a/32128977/486990
  add_compile_options(
    "$<$<CONFIG:Debug>:/MTd>" # -MTd
    "$<$<CONFIG:RelWithDebInfo>:/MT>" # -MT
    "$<$<CONFIG:Release>:/MT>" # * MT
    "$<$<CONFIG:MinSizeRel>:/MT>")
  add_compile_options("-D_CRT_SECURE_NO_WARNINGS=1")
  add_compile_options("-utf-8")
  add_compile_options("-permissive-")
  add_compile_options("-Zc:__cplusplus")
  add_compile_options("-DUNICODE=1")
  add_compile_options("-D_UNICODE=1")
  add_compile_options("-W3")
  set(CMAKE_RC_FLAGS "${CMAKE_RC_FLAGS} -c65001")
else()
  add_compile_options("-DUNICODE=1")
  add_compile_options("-D_UNICODE=1")
  add_compile_options("-g")
  add_compile_options("-Wall")
  add_compile_options("-Wextra")
  if(USE_SANITIZE)
    add_compile_options("-fsanitize=address")
    add_compile_options("-fno-omit-frame-pointer")
    set(CMAKE_EXE_LINKER_FLAGS "-fsanitize=address ")
  endif()
endif()

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)


set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules/")
# Gen version
include(VersionFromVCS)
get_source_info(${CMAKE_CURRENT_SOURCE_DIR} UNRAR_REVISION UNRAR_REMOTE_URL UNRAR_REFNAME)
string(TIMESTAMP UNRAR_COPYRIGHT_YEAR "%Y")

# UNRAR_REFNAME maybe defined by ci
if("${UNRAR_REFNAME}" STREQUAL "none")
  if(DEFINED ENV{UNRAR_REFNAME})
    set(UNRAR_REFNAME $ENV{UNRAR_REFNAME})
  endif()
endif()

if("${UNRAR_REFNAME}" MATCHES "refs/tags/*")
  string(TIMESTAMP UNRAR_BUILD_TIME "%Y-%m-%dT%H:%M:%SZ")
else()
  set(UNRAR_BUILD_TIME "none")
endif()

if(DEFINED ENV{GITHUB_RUN_NUMBER})
  set(UNRAR_VERSION_BUILD $ENV{GITHUB_RUN_NUMBER})
  message(STATUS "UNRAR detect GITHUB_RUN_NUMBER: $ENV{GITHUB_RUN_NUMBER}")
else()
  set(UNRAR_VERSION_BUILD 1)
endif()

set(UNRAR_VERSION_MAJOR 7)
set(UNRAR_VERSION_MINOR 1)
set(UNRAR_VERSION_PATCH 5)
set(PACKAGE_VERSION "${UNRAR_VERSION_MAJOR}.${UNRAR_VERSION_MINOR}.${UNRAR_VERSION_PATCH}")

string(TOLOWER "${CMAKE_C_COMPILER_ARCHITECTURE_ID}" UNRAR_ARCH_NAME)
set(CPACK_SYSTEM_NAME "win-${UNRAR_ARCH_NAME}")
set(CPACK_PACKAGE_NAME "unrar")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "unrar")
set(CPACK_PACKAGE_VERSION_MAJOR ${UNRAR_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${UNRAR_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${UNRAR_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION ${PACKAGE_VERSION})
set(CPACK_PACKAGE_VENDOR "unrar")
set(CPACK_PACKAGE_CONTACT "Alexander Roshal")
include(CPack)

add_subdirectory(unrar)