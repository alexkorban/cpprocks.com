---
title: "Using CMake to build a cross-platform project with a Boost dependency"
tags: cmake boost
---

While working on the test suite for [C++11 Rocks](/books "C++11 Rocks"), I needed to setup a cross-platform test suite based on _Boost.Test_ for code examples. I decided to go with CMake for build management because it seems to be a fairly popular option.

There are quite a few tutorials about CMake, but not many of them talk specifically about setting up cross-platform builds or handling Boost. Because of this, I had to dig through a lot of documentation, mailing list posts and so on. As I was new to CMake, I may not have set up things the best way but I thought it would be worth writing down what I learned anyway.

The platform I used was Windows 7 + Visual Studio 10. The cross-platform aspect would come in the future as I'd like to make a GCC edition of the book.

## Advantages and disadvantages

The advantages of using CMake are quite obvious. There's only one set of build scripts for both platforms, so there is no duplication! My impression was that CMake scripts are relatively easy to write. They are certainly simpler than makefiles! There is also a handy GUI for generating project files.

There are disadvantages too, of course. On the Windows side, using CMake breaks the normal workflow for someone who is used to Visual Studio. Files, projects, dependencies and settings all have to be managed outside of Visual Studio, in CMake scripts. CMake also creates extra projects to implement extra steps like installation. It definitely took some getting used to, and while working on Visual Studio examples, there were several occasions when I added files via Visual Studio and had to backtrack and do it via CMake.

## Setting up a CMake script

A CMakeScript consists of a series of commands. Here is a minimal script:

{% highlight cmake %}
cmake_minimum_required(VERSION 2.8)
project(cpp11-tests)
include_directories(src)
add_executable(test-suite test-suite.cpp)
{% endhighlight %}

The commands can be combined to form constructs such as if/elseif/else. A command can take a number of parameters and keywords which affect its behavior. Some commands are implemented with the help of _modules_. CMake comes with a large number of built-in modules.

## Cross-platform build script

You need a few things to write a cross platform script:

*   if/else construct to incorporate platform specific steps
*   ways to check what platform you’re on
*   a way to set compiler flags
*   a way to print things out for the inevitable debugging

Let’s look at an example that’s got all of these. I needed to set some compiler specific flags (e.g. to make sure exception support is enabled) which I achieved with this script:

{% highlight cmake %}
if (UNIX)
  message(status "Setting GCC flags")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fexceptions -g -Wall")
else()
  message(status "Setting MSVC flags")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /EHc-")
endif()
message(status "** CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")
{% endhighlight %}

Note the parentheses after else and endif, they are required. The message() command lets you print out variables. status is a keyword which signals what kind of message it is. Other keywords you can supply include _warning_ and _fatal_error_.

Use **CMAKE_C_FLAGS** to set compiler flags for C files, and **CMAKE_CPP_FLAGS** to set flags for C++ files.

There is a number of variables available for checking the platform. Instead of checking for **UNIX**, you could check for **WIN32**. You can also check what compiler is available using these flags (this isn't a complete list):

**MSVC**, **MSVC_IDE**, **MSVC60**, **MSVC70**, **MSVC71**, **MSVC80**, **CMAKE_COMPILER_2005**, **MSVC90**, **MSVC10** (Visual Studio 2010)

Microsoft compiler

**CMAKE_COMPILER_IS_GNUCC**

is TRUE if the compiler is a variant of gcc

**CMAKE_COMPILER_IS_GNUCXX**

is TRUE if the compiler is a variant of g++

There are more flags available for other compilers and platforms.

## Handling Boost dependency

I had to do 4 things to incorporate a dependency on Boost in my build script: 

*   Get CMake to find Boost include files
*   Link to Boost libraries (as Boost.Test requires linking)
*   Configure dynamic linking
*   Make Boost dynamic libraries available to the executable

### Find Boost

The _find_package_ command can be used to set Boost related variables:

{% highlight cmake %}
find_package(Boost 1.47 COMPONENTS filesystem unit_test_framework REQUIRED)
message(status "** Boost Include: ${Boost_INCLUDE_DIR}")
message(status "** Boost Libraries: ${Boost_LIBRARY_DIRS}")
message(status "** Boost Libraries: ${Boost_LIBRARIES}")
{% endhighlight %}

In this example I'm searching for the _filesystem_ and _unit_test_framework_ libraries from Boost. _find_package_ relies on the _FindBoost _module for knowledge about Boost. You can get more information about _FindBoost _with this command:

```
cmake --help-module FindBoost
```

_FindBoost_ module source is located in _FindBoost.cmake_ in the _Modules_ sub-folder of the CMake folder.

You may need to set your version of Boost explicitly if your version of _FindBoost_ isn't up to date: 

{% highlight cmake %}
set(Boost_ADDITIONAL_VERSIONS "1.47" "1.47.0")
{% endhighlight %}

You might also need to set the Boost root path:

{% highlight cmake %}
set(BOOST_ROOT "../boost")
{% endhighlight %}

### Linking with Boost libraries

These lines should be enough to link with Boost libraries:

{% highlight cmake %}
INCLUDE_DIRECTORIES(${Boost_INCLUDE_DIR})
LINK_DIRECTORIES(${Boost_LIBRARY_DIRS})
target_link_libraries(test-suite ${Boost_LIBRARIES})
{% endhighlight %}

If you run into issues with CMake looking for incorrect library names, this may help:
{% highlight cmake %}
add_definitions(-DBOOST_ALL_NO_LIB)  # tell the compiler to undefine this boost macro
{% endhighlight %}

This will tell the Boost config system not to automatically select which libraries to link against.

### Dynamic linking on Windows

These commands will ensure that Boost libraries are linked dynamically:

{% highlight cmake %}
set(Boost_USE_STATIC_LIBS        OFF)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME     OFF)
set(BOOST_ALL_DYN_LINK           ON)   # force dynamic linking for all libraries
{% endhighlight %}

It's also possible to configure dynamic/static linking for individual libraries.

### Making Boost dynamic libraries available to the executable

The easiest solution I found was to simply put the DLLs into the same folder with the executable. As part of the install stage, I get CMake to copy the Boost DLLs into the same folder with the executable (the code is Windows specific):

{% highlight cmake %}
	install(TARGETS cpp11-tests
		DESTINATION ../bin
		CONFIGURATIONS Release RelWithDebInfo Debug)

	get_filename_component(UTF_BASE_NAME 
          ${Boost_UNIT_TEST_FRAMEWORK_LIBRARY_RELEASE} NAME_WE)
	get_filename_component(UTF_PATH 
          ${Boost_UNIT_TEST_FRAMEWORK_LIBRARY_RELEASE} PATH)
        install(FILES ${UTF_PATH}/${UTF_BASE_NAME}.dll
          DESTINATION ../bin
          CONFIGURATIONS Release RelWithDebInfo
        )

	get_filename_component(UTF_BASE_NAME_DEBUG 
          ${Boost_UNIT_TEST_FRAMEWORK_LIBRARY_DEBUG} NAME_WE)
        install(FILES ${UTF_PATH}/${UTF_BASE_NAME_DEBUG}.dll
          DESTINATION ../bin
          CONFIGURATIONS Debug
        )
{% endhighlight %}

## Resources

I found these pages very valuable when writing my build script. 

CMake variables: [http://www.vtk.org/Wiki/CMake_Useful_Variables](http://www.vtk.org/Wiki/CMake_Useful_Variables)

CMake documentation: [http://www.cmake.org/cmake/help/cmake-2-8-docs.html](http://www.cmake.org/cmake/help/cmake-2-8-docs.html)

CMake wiki: [http://www.vtk.org/Wiki/CMake](http://www.vtk.org/Wiki/CMake)