cmake_minimum_required(VERSION 3.9)
project(sds-lk VERSION 2.1 DESCRIPTION "simple dynamic strings library")

add_library(sds SHARED sds.c)
target_include_directories(sds PUBLIC .)
set_target_properties(sds PROPERTIES
    CMAKE_C_STANDARD C99
    VERSION ${PROJECT_VERSION}
)

add_executable(sds-test sds.c)
target_include_directories(sds-test PUBLIC sds)
target_compile_definitions(sds-test PUBLIC SDS_TEST_MAIN)
set_target_properties(sds-test PROPERTIES
    CMAKE_C_STANDARD C99
    EXCLUDE_FROM_ALL TRUE
)

foreach(target sds sds-test)
    if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
        target_compile_options(${target} PUBLIC /W4)

        # Disable warnings
        target_compile_options(${target} PUBLIC
            # nonstandard extension used: zero-sized array in struct/union
            # This is C99 but MSVC doesn't know about new C versions. (Not that
            # a 20-year old standard is even new...)
            /wd4200

            # Possible loss of data when converting between different integral
            # types. We're smart and bounds checking is done beforehand so this
            # is okay.
            /wd4267 /wd4242 /wd4244

            # Variable shadowing.
            /wd4456
        )
    else ()
        target_compile_options(${target} PUBLIC -Wall -Wextra -Wpedantic)
    endif()
endforeach()
