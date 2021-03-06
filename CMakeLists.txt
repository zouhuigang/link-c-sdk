
cmake_minimum_required (VERSION 2.8)

project (link-c-sdk)

message( "CMAKE_C_COMPILER is " ${CMAKE_C_COMPILER} )
set( COMPILER 0 )
string( REGEX MATCH cc|mips[a-zA-Z]+-gcc|arm[-a-zA-Z]*-gcc COMPILER ${CMAKE_C_COMPILER})
message( "COMPILER is " ${COMPILER} )
#string( LENGTH ${COMPILER} LEN )
set( POS 0 )
if ( NOT ${COMPILER} STREQUAL cc )
    string( FIND ${COMPILER} -gcc POS )
endif()
message("POS IS " ${POS} )

string( FIND ${CMAKE_C_COMPILER} "CommandLineTools" MACPOS )
string( FIND ${CMAKE_C_COMPILER} "/usr/bin/" LINUXPOS )
message( "POS IS " ${POS} )
set( HOST "x86_64-linux" )
if ( ${POS} GREATER 0 )
    string( SUBSTRING ${COMPILER} 0 ${POS} HOST )
# if the cc tool path contain "CommandLineTools", we guress it is in mac os
elseif( ${MACPOS} GREATER 0 )
    set( HOST "x86_64-apple-darwin" )
# if the cc tool path contail "/usr/bin/", we guess it is in linux os
elseif( ${LINUXPOS} GREATER 0 )
    set ( HOST "x86_64-linux")
endif()
message( "HOST is " ${HOST} )

add_custom_target( all-clean make -C $ENV{PWD}/../third_party/curl-7.61.1 clean )

SET(GCC_COVERAGE_COMPILE_FLAGS "-Wno-unused")
if (WITH_DEBUG)
    message("WITH_DEBUG")
    add_definitions("-Wall -g")
endif()

add_subdirectory(third_party)
add_subdirectory(libtsuploader)
add_subdirectory(demo)
