# Copyright (c) Tribufu. All Rights Reserved.
# SPDX-License-Identifier: UNLICENSED

cmake_minimum_required(VERSION 3.20)

project(lua LANGUAGES C)

option(LUA_BUILD_COMPILER "Build lua compiler" OFF)
option(LUA_BUILD_INTERPRETER "Build lua interpreter" OFF)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/bin/$<LOWER_CASE:$<CONFIG>>/${MTK_PRESET_NAME}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/bin/$<LOWER_CASE:$<CONFIG>>/${MTK_PRESET_NAME}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/bin/$<LOWER_CASE:$<CONFIG>>/${MTK_PRESET_NAME}")

set(LUA_CORE_SOURCES
    lapi.c
    lcode.c
    lctype.c
    ldebug.c
    ldo.c
    ldump.c
    lfunc.c
    lgc.c
    llex.c
    lmem.c
    lobject.c
    lopcodes.c
    lparser.c
    lstate.c
    lstring.c
    ltable.c
    ltm.c
    lundump.c
    lvm.c
    lzio.c
)

set(LUA_LIB_SOURCES
    lauxlib.c
    lbaselib.c
    lcorolib.c
    ldblib.c
    linit.c
    liolib.c
    lmathlib.c
    loadlib.c
    loslib.c
    lstrlib.c
    ltablib.c
    lutf8lib.c
)

add_library(lua STATIC
    ${LUA_CORE_SOURCES}
    ${LUA_LIB_SOURCES}
)

add_library(Lua::Lua ALIAS lua)

target_include_directories(lua PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

target_compile_features(lua PUBLIC c_std_99)

if(UNIX AND NOT APPLE AND NOT EMSCRIPTEN)
    target_link_libraries(lua PUBLIC m dl)
elseif(UNIX OR EMSCRIPTEN)
    target_link_libraries(lua PUBLIC m)
endif()

if(APPLE)
    target_compile_definitions(lua PRIVATE LUA_USE_MACOSX)
elseif(UNIX AND NOT EMSCRIPTEN)
    target_compile_definitions(lua PRIVATE LUA_USE_LINUX)
endif()

if(LUA_BUILD_INTERPRETER)
    add_executable(lua_interpreter lua.c)
    target_link_libraries(lua_interpreter PRIVATE lua)
endif()

if(LUA_BUILD_COMPILER)
    add_executable(luac luac.c)
    target_link_libraries(luac PRIVATE lua)
endif()
