
Finding Packages
================

If a FindXXX.cmake file for the library you are packaging is already available, it should work automatically.

Variables **CMAKE_INCLUDE_PATH** and **CMAKE_LIBRARY_PATH** are set with the correct requirements paths.
The CMake **find_library** function will be able to locate the libraries in the package's folders.

Allowing you to use **find_package**:


.. code-block:: cmake

    project(MyHello)
    cmake_minimum_required(VERSION 2.8.12)

    include(conanbuildinfo.cmake)
    conan_basic_setup()

    find_package("ZLIB")

    if(ZLIB_FOUND)
        add_executable(enough enough.c)
        include_directories(${ZLIB_INCLUDE_DIRS})
        target_link_libraries(enough ${ZLIB_LIBRARIES})
    else()
        message(FATAL_ERROR "Zlib not found")
    endif()


In addition to automatic **find_package** support, the **CMAKE_MODULE_PATH** variable is set with your requirements root package paths.
You can override the default behavior of any find_package() by creating a ``findXXX.cmake`` file in your package.







Creating A Custom FindXXX.cmake File
------------------------------------

Sometimes the "official" CMake FindXXX.cmake scripts are not ready to find our libraries (not supported library names for specific settings, fixed installation directories like C:\\OpenSSL... etc)
or perhaps there is no "official" CMake script for our library.

In these cases, we can provide a custom **FindXXX.cmake** file in our Conan packages.

1. Create a file named FindXXX.cmake and save it to your Conan package root folder. Where XXX is the name of the library that we will use in the **find_package** CMake function.
For example, we can create a ``FindZLIB.cmake`` and use ``find_package(ZLIB)``.
We recommend copying the original FindXXX.cmake file from Kitware (folder Modules/FindXXX.cmake), if available, and modifying it to help finding our library files, but it depends a lot. Alternatively, you may be interested in creating a new one.

If it's not provided you can create a basic one, take a look at this example with the ZLIB library:

**FindZLIB.cmake**

.. code-block:: cmake

   find_path(ZLIB_INCLUDE_DIR NAMES zlib.h PATHS ${CONAN_INCLUDE_DIRS_ZLIB})
   find_library(ZLIB_LIBRARY NAMES ${CONAN_LIBS_ZLIB} PATHS ${CONAN_LIB_DIRS_ZLIB})

   set(ZLIB_FOUND TRUE)
   set(ZLIB_INCLUDE_DIRS ${ZLIB_INCLUDE_DIR})
   set(ZLIB_LIBRARIES ${ZLIB_LIBRARY})
   mark_as_advanced(ZLIB_LIBRARY ZLIB_INCLUDE_DIR)


In the first line we are finding the path where our headers should be found, we suggest the CONAN_INCLUDE_DIRS_XXX.
Then the same for the library names with CONAN_LIBS_XXX and the paths where the libs are CONAN_LIB_DIRS_XXX.

2. In your conanfile.py file, add the ``FindXXX.cmake`` to the ``exports_sources`` field:


.. code-block:: python

   class HelloConan(ConanFile):
       name = "Hello"
       version = "0.1"
       ...
       exports_sources = ["FindXXX.cmake"]

3. In the package method, copy the ``FindXXX.cmake`` file to the root:



.. code-block:: python

   class HelloConan(ConanFile):
       name = "Hello"
       version = "0.1"
       ...
       exports_sources = ["FindXXX.cmake"]


       def package(self):
           ...
           self.copy("FindXXX.cmake", ".", ".")



.. _`conan's boost package`: https://github.com/lasote/conan-boost.git
.. _`conan's zlib package`: https://github.com/lasote/conan-zlib.git
