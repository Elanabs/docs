
``cmake`` Generator
===================

If you are using **CMake** to build your project, you can use the ``cmake`` generator to define all your requirement information in cmake syntax.
It creates a file called ``conanbuildinfo.cmake`` that can be imported from your ``CMakeLists.txt``.


**conanfile.txt**

.. code-block:: text

   ...

   [generators]
   cmake


When **conan install** is executed, a file named ``conanbuildinfo.cmake`` is created.

You can include ``conanbuildinfo.cmake`` in your ``CMakeLists.txt`` project to manage your requirements.
The inclusion of ``conanbuildinfo.cmake`` doesn't alter the cmake environment at all, it just provides ``CONAN_`` variables and some useful macros.


Global Variables Approach
-------------------------

The simplest way to consume it would be to invoke the ``conan_basic_setup()`` macro, which will basically
set global include directories, libraries directories, definitions, etc. so typically is enough to do:

.. code-block:: cmake

    include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    conan_basic_setup()

    add_executable(timer timer.cpp)
    target_link_libraries(timer ${CONAN_LIBS})

The ``conan_basic_setup()`` is split into smaller macros, that should be self explanatory. If you need to do
something different, you can just use them individually.


Targets Approach
----------------

For **modern cmake (>=3.1.2)**, you can use the following approach:

.. code-block:: cmake

    include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    conan_basic_setup(TARGETS)

    add_executable(timer timer.cpp)
    target_link_libraries(timer CONAN_PKG::Poco)
    
Using ``TARGETS`` as an argument, will cause ``conan_basic_setup()`` to internally call the macro ``conan_define_targets()``
which defines the cmake ``INTERFACE IMPORTED`` targets, one per package. These targets, named ``CONAN_PKG::PackageName`` can be used to link with, instead of using the global cmake setup.


.. seealso:: Check the section :ref:`Reference/Generators/cmake <cmake_generator>` to read more about this generator.

