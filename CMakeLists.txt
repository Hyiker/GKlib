cmake_minimum_required(VERSION 2.8)
project(GKlib C)

option(GKLIB_SHARED "Build shared libraries (.dll/.so) instead of static ones (.lib/.a)" OFF)
option(GKLIB_BUILD_TESTS "Build GKlib tests" OFF)
option(GKLIB_INSTALL "Install GKlib" ON)

get_filename_component(abs "." ABSOLUTE)
unset(abs)
set(GKLIB_PATH ${CMAKE_CURRENT_SOURCE_DIR})
include(GKlibSystem.cmake)

if(GKLIB_SHARED)
    set(GKLIB_LIBRARY_TYPE SHARED)
else(GKLIB_SHARED)
    set(GKLIB_LIBRARY_TYPE STATIC)
endif(GKLIB_SHARED)

add_library(GKlib ${GKLIB_LIBRARY_TYPE} ${GKlib_sources})

if(MSVC)
    include_directories("win32")
    file(GLOB win32_sources RELATIVE "win32" "*.c")
    target_sources(GKlib PRIVATE ${win32_sources})
else(MSVC)
    set(win32_sources, "")
endif(MSVC)

target_include_directories(GKlib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

if(UNIX)
    target_link_libraries(GKlib m)
endif(UNIX)

if(GKLIB_BUILD_TESTS)
    include_directories("test")
    add_subdirectory("test")
endif(GKLIB_BUILD_TESTS)

if(GKLIB_INSTALL)
    install(TARGETS GKlib
        ARCHIVE DESTINATION lib/${LINSTALL_PATH}
        LIBRARY DESTINATION lib/${LINSTALL_PATH})
    install(FILES ${GKlib_includes} DESTINATION include/${HINSTALL_PATH})
endif(GKLIB_INSTALL)
