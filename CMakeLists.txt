CMAKE_MINIMUM_REQUIRED(VERSION 2.8.8)

# Get the project name in capital letters
# for the logging macros
string(TOUPPER ${PROJECT_NAME} PROJECT_NAME_CAPS)
string(TOUPPER ${CMAKE_BUILD_TYPE} LSPDLOG_BUILD_TYPE)

if(LSPDLOG_BUILD_TYPE MATCHES DEBUG)
  set(_LSPDLOG_ENABLE_DEBUG_ true)
else()
  set(_LSPDLOG_ENABLE_DEBUG_ false)
  set(LSPDLOG_ENABLE_TRACE_LOGGING false)
endif()

if(LSPDLOG_ENABLE_TRACE_LOGGING)
  set(_LSPDLOG_ENABLE_TRACE_LOGGING_ true)
else()
  set(_LSPDLOG_ENABLE_TRACE_LOGGING_ false)
endif()

include (CheckCXXSourceCompiles)
set(CMAKE_REQUIRED_FLAGS "-std=c++11")
CHECK_CXX_SOURCE_COMPILES(
"
  #include <tuple>
  constexpr auto repeated_brace = std::make_tuple(\"{}\",\"{}{}\");
  int main(void) { return 0; }
"
_LSPDLOG_COMPILER_SUPPORTS_CONSTEXPR_FUNC_)

# Download spdlog
add_subdirectory(internal)

# Set C++11 support
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)

if(NOT COMPILER_SUPPORTS_CXX11)
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
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/internal/wrapper.h.in "${CONFIG_DIR}/wrapper.h")

set(${PROJECT_NAME}_LSPDLOG_INCLUDE_DIRS
    ${LSPDLOG_INCLUDE_DIRS}
    ${PROJECT_BINARY_DIR}/lspdlog/config PARENT_SCOPE)
