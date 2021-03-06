cmake_minimum_required(VERSION 2.8)
project(cio C)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

string(COMPARE EQUAL "${CMAKE_SYSTEM_NAME}" "Linux" is_linux)
if(is_linux)
    include_directories(${CMAKE_CURRENT_SOURCE_DIR}/linux/)
    set(CIO_LINUX_FILES
        linux/cio_linux_epoll.c
        linux/cio_linux_server_socket.c
    )
endif()

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/)
set(CIO_FILES
)

if(CMAKE_C_COMPILER_ID STREQUAL "GNU" OR CMAKE_C_COMPILER_ID STREQUAL "Clang")
    set(WARN_SWITCHES "-Wall -Wextra -Werror -Wshadow -Winit-self -Wcast-qual -Wcast-align -Wformat=2 -Wwrite-strings -Wmissing-prototypes -Wstrict-overflow=5 -pedantic")
    set(CMAKE_C_FLAGS "-pipe -fno-common ${WARN_SWITCHES}")
    set(CMAKE_C_FLAGS_RELEASE "-fno-asynchronous-unwind-tables ${CMAKE_C_FLAGS_RELEASE}")
    set_source_files_properties(
        ${CIO_FILES} PROPERTIES COMPILE_FLAGS "-std=c99"
    )
endif()

add_library(cio_static
    ${CIO_FILES}
    ${CIO_LINUX_FILES}
)
set_target_properties(cio_static
    PROPERTIES OUTPUT_NAME "cio"
)

add_library(cio SHARED
    ${CIO_FILES}
    ${CIO_LINUX_FILES}
)
set_target_properties(cio
    PROPERTIES OUTPUT_NAME "cio"
)

add_subdirectory(linux/tests/) 

enable_testing()

