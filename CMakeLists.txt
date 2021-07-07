cmake_minimum_required(VERSION 3.14)
project(mpdecimal)

option(mpdecimal_STATIC "If it should be compiled static or dynamic" ON)
option(mpdecimal_EXAMPLES "If it should be compiled static or dynamic" ON)



set(mpdecimal_LIBRARY_TYPE DYNAMIC)
if(mpdecimal_STATIC)
    set(mpdecimal_LIBRARY_TYPE STATIC)
endif()

# GoogleTest requires at least C++11
set(CMAKE_CXX_STANDARD 11)

# Update from: https://www.bytereef.org/mpdecimal/download.html
include(FetchContent)
FetchContent_Declare(
        mpdecimal
        URL https://www.bytereef.org/software/mpdecimal/releases/mpdecimal-2.5.1.zip
)
FetchContent_MakeAvailable(mpdecimal)

set(mpdecimalc_SOURCE_DIR ${mpdecimal_SOURCE_DIR}/libmpdec)
set(mpdecimalpp_SOURCE_DIR ${mpdecimal_SOURCE_DIR}/libmpdec++)

if(_MSC_VER)
    add_compile_definitions(MASM)
else()
    enable_language(C ASM)
    add_compile_definitions(ASM)

endif()

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    add_compile_definitions(CONFIG_64)
elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
    # 32 bits
    add_compile_definitions(CONFIG_32)
endif()

set(mbdecimal_HEADERS
        ${mpdecimalc_SOURCE_DIR}/basearith.h
        ${mpdecimalc_SOURCE_DIR}/bits.h
        ${mpdecimalc_SOURCE_DIR}/constants.h
        ${mpdecimalc_SOURCE_DIR}/convolute.h
        ${mpdecimalc_SOURCE_DIR}/crt.h
        ${mpdecimalc_SOURCE_DIR}/difradix2.h
        ${mpdecimalc_SOURCE_DIR}/fnt.h
        ${mpdecimalc_SOURCE_DIR}/fourstep.h
        ${mpdecimalc_SOURCE_DIR}/io.h
        ${mpdecimalc_SOURCE_DIR}/mpalloc.h
        ${mpdecimalc_SOURCE_DIR}/mpdecimal32vc.h
        ${mpdecimalc_SOURCE_DIR}/mpdecimal64vc.h
        ${mpdecimalc_SOURCE_DIR}/numbertheory.h
        ${mpdecimalc_SOURCE_DIR}/sixstep.h
        ${mpdecimalc_SOURCE_DIR}/transpose.h
        ${mpdecimalc_SOURCE_DIR}/typearith.h
        ${mpdecimalc_SOURCE_DIR}/umodarith.h

        # C++
        ${mpdecimalpp_SOURCE_DIR}/decimal.cc
        )


set(mbdecimal_SOURCES
        ${mpdecimalc_SOURCE_DIR}/basearith.c
        ${mpdecimalc_SOURCE_DIR}/constants.c
        ${mpdecimalc_SOURCE_DIR}/context.c
        ${mpdecimalc_SOURCE_DIR}/mpdecimal.c
        ${mpdecimalc_SOURCE_DIR}/convolute.c
        ${mpdecimalc_SOURCE_DIR}/crt.c
        ${mpdecimalc_SOURCE_DIR}/difradix2.c
        ${mpdecimalc_SOURCE_DIR}/fnt.c
        ${mpdecimalc_SOURCE_DIR}/fourstep.c
        ${mpdecimalc_SOURCE_DIR}/io.c
        ${mpdecimalc_SOURCE_DIR}/mpalloc.c
        ${mpdecimalc_SOURCE_DIR}/numbertheory.c
        ${mpdecimalc_SOURCE_DIR}/sixstep.c
        ${mpdecimalc_SOURCE_DIR}/transpose.c
        
        # C++
        ${mpdecimalpp_SOURCE_DIR}/decimal.cc
)



add_library(mpdecimal ${mpdecimal_LIBRARY_TYPE} ${mbdecimal_SOURCES} ${mbdecimal_HEADERS})
target_include_directories(mpdecimal
        PUBLIC ${mpdecimalpp_SOURCE_DIR} ${mpdecimalc_SOURCE_DIR}
)

message(${mpdecimalpp_SOURCE_DIR} ${mpdecimalc_SOURCE_DIR})

if(mpdecimal_EXAMPLES)
    add_executable(mpdecimal_tests_factorial ${mpdecimalpp_SOURCE_DIR}/examples/factorial.cc)
    target_link_libraries(mpdecimal_tests_factorial PRIVATE mpdecimal)

    add_executable(mpdecimal_tests_pi ${mpdecimalpp_SOURCE_DIR}/examples/pi.cc)
    target_link_libraries(mpdecimal_tests_pi PRIVATE mpdecimal)
endif()