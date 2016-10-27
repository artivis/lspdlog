CMAKE_MINIMUM_REQUIRED(VERSION 2.8.8)

# Get the project name in capital letters
# for the logging macros
string(TOUPPER ${PROJECT_NAME} PROJECT_NAME_CAPS)

if(CMAKE_BUILD_TYPE MATCHES DEBUG)
  set(__AM_I_COMPILED_IN_DEBUG__ true)
else()
  set(__AM_I_COMPILED_IN_DEBUG__ false)
endif()

# Download spdlog
add_subdirectory(internal)

# Set C++11 support
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)

if(COMPILER_SUPPORTS_CXX11)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11" PARENT_SCOPE)
else()
  message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support.\
          Please use a different C++ compiler.")
endif()

# Set lsdplog/logging.h base directory
set(CONFIG_DIR "${PROJECT_BINARY_DIR}/lspdlog/config/lspdlog")

# Create the specified output directory if it does not exist.
if (NOT EXISTS "${CONFIG_DIR}")
  message(STATUS "Creating config output directory: ${CONFIG_DIR}")
  file(MAKE_DIRECTORY "${CONFIG_DIR}")
endif()
if (EXISTS "${CONFIG_DIR}" AND NOT IS_DIRECTORY "${CONFIG_DIR}")
  message(FATAL_ERROR "Bug: Specified CONFIG_DIR: "
    "${CONFIG_DIR} exists, but is not a directory.")
endif()

# configure logging.h
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/internal/logging.h.in "${CONFIG_DIR}/logging.h")

set(LSPDLOG_INCLUDE_DIRS
    ${LSPDLOG_INCLUDE_DIRS}
    ${PROJECT_BINARY_DIR}/lspdlog/config PARENT_SCOPE)
