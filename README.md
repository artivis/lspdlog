# lspdlog - Lazy spdlog

============================================

A simple wrapper package to enable cool logging for your project. This package is nothing but a tiny hacky wrapper around [spdlog](https://github.com/gabime/spdlog) logging library. It has no other aim than enabling a bunch of macros to ease logging for your project. Currently only support C++.

For a more customizable logging please refer directly to [spdlog wiki](https://github.com/gabime/spdlog/wiki/1.-QuickStart).

## Disclamer

All credits go to Gabi Melman and [spdlog](https://github.com/gabime/spdlog) contributors, for this super fast C++ logging library. Forked from [lspdlog](https://github.com/artivis/lspdlog), because it was not maintained and still using the old lib, this project reuse the `over-engineered` from original lspdlog and make it shine again.

## Requirements

- c++11 compliant compiler
- cmake definition

## Install

1. Simply clone this package as the submodule in your project directory:

```bash
$ cd /path/project/directory/
$ git submodule add https://github.com/nodefluxio/lspdlog.git
```

2. In the project `CMakeLists.txt`:

```cmake
project(my_project)
option(LSPDLOG_ENABLE_TRACE_LOGGING "Enable trace logging." ON)
find_package(Threads REQUIRED)
add_subdirectory(lspdlog)
include_directories(${LSPDLOG_INCLUDE_DIRS})
add_executable(my_project my_project.cpp)
target_link_libraries(my_project ${CMAKE_THREAD_LIBS_INIT})
```

3. Build the project.

4. Nop that's it.

## Logging Levels

This package defines automatically the following macros:

```cpp
MY_PROJECT_NAME_INFO(...);
MY_PROJECT_NAME_WARN(...);
MY_PROJECT_NAME_ERROR(...);
MY_PROJECT_NAME_CRITICAL(...);
```

### Debug Mode

```cpp
MY_PROJECT_NAME_DEBUG(...);
MY_PROJECT_NAME_TRACE(...); // can be turned off as shown in example
```

### Usage Example:

Given your project `CMakeLists.txt`:

```cmake
project(my_awesome_project)

# Set 'ON' to enable 'TRACE' macro.
option(LSPDLOG_ENABLE_TRACE_LOGGING "Enable trace logging." OFF)

find_package(Threads REQUIRED)                         #<-- necessary

# lspdlog & spdlog require c++11                       #<-- necessary and
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")  #<-- automatically set

SET(CMAKE_BUILD_TYPE "DEBUG")

add_subdirectory(lspdlog)                    #<-- necessary
include_directories(${LSPDLOG_INCLUDE_DIRS}) #<-- necessary

add_executable(my_project my_project.cpp)
add_dependencies(my_project spdlog) #<-- necessary, waits for downloading spdlog
target_link_libraries(my_project ${CMAKE_THREAD_LIBS_INIT})  #<-- necessary
```

and your main file `my_project.cpp`:

```cpp
#include "lspdlog/logging.h"

int main()
{
  std::cout << "Hello world." << std::endl;

  MY_PROJECT_INFO("Yep ", "that is");
  MY_PROJECT_WARN("way\t", 2);
  MY_PROJECT_DEBUG("(I meant 'too')");
  MY_PROJECT_ERROR("easy");
  MY_PROJECT_CRITICAL("peasy");
  MY_PROJECT_TRACE("lemon squeezy");

  return 0;
}
```

the following gets printed in your terminal:

```bash
Hello world.
[2019-07-16 10:56:44.440903867] [   info   ] [my_project] Yep that is
[2019-07-16 10:56:44.441016740] [ warning  ] [my_project] way   2
[2019-07-16 10:56:44.441020164] [  debug   ] [my_project] (I meant 'too')
[2019-07-16 10:56:44.441038220] [  error   ] [my_project] easy
[2019-07-16 10:56:44.441040957] [ critical ] [my_project] peasy
[2019-07-16 10:56:44.441053625] [  trace   ] [my_project] [main.cpp, ln.14 : main()] lemon squeezy
```

Now if we re-compile in release:

```cmake
SET(CMAKE_BUILD_TYPE "RELEASE")
```

```bash
Hello world.
[2019-07-16 10:56:44.440903867] [   info   ] [my_project] Yep that is
[2019-07-16 10:56:44.441016740] [ warning  ] [my_project] way   2
[2019-07-16 10:56:44.441038220] [  error   ] [my_project] easy
[2019-07-16 10:56:44.441040957] [ critical ] [my_project] peasy
```

# Todo

- [x] trace macro
- [x] scoped log macro
- [x] critical macro
- [ ] fix formatter
- [ ] enable more customization
