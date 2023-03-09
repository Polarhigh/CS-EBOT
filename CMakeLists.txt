cmake_minimum_required(VERSION 3.10)
project(ebot)

set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
set(CMAKE_CXX_STANDARD 11)

add_library(${PROJECT_NAME} SHARED
        source/basecode.cpp
        source/callbacks.cpp
        source/chatlib.cpp
        source/combat.cpp
        source/control.cpp
        source/engine.cpp
        source/globals.cpp
        source/interface.cpp
        source/navigate.cpp
        source/netmsg.cpp
        source/precomp.cpp
        source/support.cpp
        source/waypoint.cpp)

target_include_directories(${PROJECT_NAME} PUBLIC
        include)

target_precompile_headers(${PROJECT_NAME} PUBLIC
        include/core.h)

#-----------
# compiler & linker options
#-----------
if (UNIX)
    target_compile_options(${PROJECT_NAME} PRIVATE -fpermissive)

    if (CMAKE_BUILD_TYPE MATCHES Release)
        target_compile_options(${PROJECT_NAME} PRIVATE -O2)
    endif()
endif()

if (MSVC)
    target_compile_options(${PROJECT_NAME} PRIVATE /GR-)

    if (CMAKE_BUILD_TYPE MATCHES Release)
        target_compile_options(${PROJECT_NAME} PRIVATE /Zi /O2)

        set_target_properties(${PROJECT_NAME} PROPERTIES
                LINK_FLAGS "/LTCG /INCREMENTAL:NO /DEBUG /OPT:REF /OPT:ICF /MAP:${CMAKE_BINARY_DIR}/${PROJECT_NAME}.map")

        set_target_properties(${PROJECT_NAME} PROPERTIES
                COMPILE_PDB_NAME ${PROJECT_NAME}
                COMPILE_PDB_OUTPUT_DIR ${CMAKE_BINARY_DIR})
    endif()
endif()

#-----------
# definitions
#-----------

target_compile_definitions(${PROJECT_NAME} PUBLIC
        NOMINMAX)

if (WIN32)
    target_compile_definitions(${PROJECT_NAME} PUBLIC
            _WINDOWS
            _WIN32
            _WIN32_WINNT=0x0600
            WIN32
            WINVER=0x0600)
endif()

if (CMAKE_BUILD_TYPE MATCHES Debug)
    target_compile_definitions(${PROJECT_NAME} PUBLIC _DEBUG SOURCE_DEBUG)
else()
    target_compile_definitions(${PROJECT_NAME} PUBLIC NDEBUG)
endif()

#-----------
# libraries
#-----------

if (WIN32)
    target_link_libraries(${PROJECT_NAME}
            Ws2_32.lib)
else()
    target_link_libraries(${PROJECT_NAME}
            -static-libstdc++
            -static-libgcc)
endif()