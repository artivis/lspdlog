# lspdlog - Lazy spdlog

## A simple wrapper package to enable cool logging for your project.

This package is nothing but a tiny hacky wrapper around [spdlog](https://github.com/gabime/spdlog) logging library.  
It has no other aim than enabling a bunch of macros to ease logging for your project.

For a more customizable logging please refer directly to [spdlog wiki](https://github.com/gabime/spdlog/wiki/1.-QuickStart).

##### Disclamer:

All credits go to Gabi Melman and [spdlog](https://github.com/gabime/spdlog) contributors, for this super fast C++ logging library.  
I have only over-engineered a tiny `cmake/cpp` layer on top of it.

## Requirements

-   c++11 compliant compiler.
-   internet connexion (spdlog is downloaded from [github](https://github.com/gabime/spdlog)).

## Install

1.  Simply clone or copy this package in your project directory:

    ```terminal
    $ cd ~/your_project/root_directory/
    $ git clone https://github.com/artivis/lspdlog.git
    ```

2.  In your project `CMakeLists.txt`:

    ```cmake
    find_package(Threads REQUIRED)

    add_subdirectory(lspdlog)
    include_directories(${LSPDLOG_INCLUDE_DIRS})

    add_executable(my_project my_project.cpp)
    add_dependencies(my_project spdlog)
    target_link_libraries(my_project
        ${CMAKE_THREAD_LIBS_INIT})
    ```

3.  Compile your project.

4.  Nop that's it.

## Now what ?

This package defines automatically the following macros:

```cpp
YOUR_PROJECT_NAME_INFO(...);
YOUR_PROJECT_NAME_WARN(...);
YOUR_PROJECT_NAME_DEBUG(...);
YOUR_PROJECT_NAME_ERROR(...);
```

Given your project `CMakeLists.txt`:

```cmake
project(my_awesome_project)

find_package(Threads REQUIRED)                         #<-- necessary

# lspdlog & spdlog require c++11                       #<-- necessary and
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")  #<-- automatically set

SET(CMAKE_BUILD_TYPE "DEBUG")

add_subdirectory(lspdlog)                    #<-- necessary
include_directories(${LSPDLOG_INCLUDE_DIRS}) #<-- necessary

add_executable(my_project my_project.cpp)
add_dependencies(my_project spdlog) #<-- necessary, waits for downloading spdlog
target_link_libraries(my_project
    ${CMAKE_THREAD_LIBS_INIT})     #<-- necessary
```

and you main `my_project.cpp`:

```cpp
#include "lspdlog/logging.h"

// Doable but not necessary
//#include "spdlog/spdlog.h"

int main()
{
  std::cout << "Hello world." << std::endl;

  MY_AWESOME_PROJECT_INFO("Yep ", "that is");
  MY_AWESOME_PROJECT_WARN("way\t", 2);
  MY_AWESOME_PROJECT_DEBUG("(I meant 'too'");
  MY_AWESOME_PROJECT_ERROR("!", "!", "!");

  return 0;
}
```

the following gets printed in your terminal:

```terminal
Hello world.
[info] Yep that is
[warning] way   2
[debug] (I meant 'too')
[error] easy
```

Now if we re-compile in release:

```cmake
SET(CMAKE_BUILD_TYPE "RELEASE")
```

```terminal
Hello world.
[info] Yep that is
[warning] way   2
[error] easy
```

However you can still manually enable/disable debug messages with:

```cpp
MY_AWESOME_PROJECT_ENABLE_DEBUG_LOG();
MY_AWESOME_PROJECT_DEBUG("will get printed.");

MY_AWESOME_PROJECT_DISABLE_DEBUG_LOG();
MY_AWESOME_PROJECT_DEBUG("will NOT get printed.");
```

# Todo

-   [ ] fix (do) install rules & `ExternalProject_Add`
-   [ ] trace & critical macro
-   [ ] async logger
-   [ ] enable more customization
