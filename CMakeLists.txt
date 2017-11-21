
cmake_minimum_required (VERSION 3.8.0) 
project(CMakeBug)

set(CMAKE_VERBOSE_MAKEFILE ON)

add_library( libFoo INTERFACE )
if( ${CMAKE_GENERATOR} MATCHES "Visual Studio.*")
    target_compile_options( libFoo INTERFACE /FIfoo.h )
elseif(${CMAKE_GENERATOR} MATCHES "Unix Makefiles")
    target_compile_options( libFoo INTERFACE -include "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/foo.h>" $<INSTALL_INTERFACE:install/foo.h>  )
endif()

add_executable( bar main.cpp bar.h)
target_link_libraries( bar PUBLIC libFoo )

if( ${CMAKE_GENERATOR} MATCHES "Visual Studio.*")

    set_property( SOURCE main.cpp APPEND_STRING PROPERTY COMPILE_FLAGS " /FIbar.h" )    # "/FIfoo.h" is lost

elseif(${CMAKE_GENERATOR} MATCHES "Unix Makefiles")

    set_property( SOURCE main.cpp APPEND_STRING PROPERTY COMPILE_FLAGS "-include \"${CMAKE_CURRENT_SOURCE_DIR}/bar.h\"" )   # works as expected

endif()
