cmake_minimum_required(VERSION 3.4)
# project name and language
project(lzmasdk)

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

cmake_policy(SET CMP0054 NEW)

# enable testing
enable_testing()

# check build type
if (NOT CMAKE_BUILD_TYPE)
    message(STATUS "No build type selected, default to Debug")
    set(CMAKE_BUILD_TYPE "Debug")
endif()

# make uppercase string of build type
string(TOUPPER "${CMAKE_BUILD_TYPE}" uppercase_CMAKE_BUILD_TYPE)

# check build type again
if (NOT uppercase_CMAKE_BUILD_TYPE MATCHES "^(DEBUG|RELEASE|RELWITHDEBINFO|MINSIZEREL)$")
    message(FATAL_ERROR "Invalid value for CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")
endif()

## set output directory (build/)
#set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/build)
#set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
#set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})


if (WIN32)
    # enable Win32 resource
    enable_language(RC)

    if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
        # using Clang
        set(CMAKE_C_FLAGS "-static")
        set(CMAKE_CXX_FLAGS "-static")
    elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        # using GCC
        set(CMAKE_C_FLAGS "-static -static-libgcc -static-libstdc++")
        set(CMAKE_CXX_FLAGS "-static -static-libgcc -static-libstdc++")
    elseif (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
        # replace "/MD" with "/MT" (building without runtime DLLs)
        set(CompilerFlags
            CMAKE_C_FLAGS
            CMAKE_C_FLAGS_DEBUG
            CMAKE_C_FLAGS_RELEASE
            CMAKE_C_FLAGS_RELWITHDEBINFO
            CMAKE_CXX_FLAGS
            CMAKE_CXX_FLAGS_DEBUG
            CMAKE_CXX_FLAGS_RELEASE
            CMAKE_CXX_FLAGS_RELWITHDEBINFO)
        foreach(CompilerFlags ${CompilerFlags})
            string(REPLACE "/MD" "/MT" ${CompilerFlags} "${${CompilerFlags}}")
        endforeach()
    endif()
endif()

##############################################################################

add_executable(7zDec
  src/C/7zAlloc.c
  src/C/7zBuf.c
  src/C/7zCrc.c
  src/C/7zCrcOpt.c
  src/C/7zFile.c
  src/C/7zDec.c
  src/C/7zArcIn.c
  src/C/7zStream.c
  src/C/Bcj2.c
  src/C/Bra.c
  src/C/Bra86.c
  src/C/BraIA64.c
  src/C/CpuArch.c
  src/C/Delta.c
  src/C/Lzma2Dec.c
  src/C/LzmaDec.c
  src/C/Ppmd7.c
  src/C/Ppmd7Dec.c
  src/C/Util/7z/7zMain.c
  src/C/Util/7z/Precomp.c)

add_library(lzmasdk STATIC
  src/C/Util/Lzma/LzmaUtil.c
  src/C/Alloc.c
  src/C/LzFind.c
  src/C/LzFindMt.c
  src/C/LzmaDec.c
  src/C/LzmaEnc.c
  src/C/7zFile.c
  src/C/7zStream.c
  src/C/Threads.c
  src/C/ThreadsP.c)

add_library(lzmalib SHARED
  src/C/Util/LzmaLib/LzmaLibExports.c
  src/C/Util/LzmaLib/LzmaLib.def
  src/C/Alloc.c
  src/C/LzFind.c
  src/C/LzFindMt.c
  src/C/LzmaDec.c
  src/C/LzmaEnc.c
  src/C/LzmaLib.c
  src/C/Threads.c
  src/C/ThreadsP.c
  src/C/Util/LzmaLib/resource.rc)

set_target_properties(lzmalib PROPERTIES OUTPUT_NAME "lzmalib")
set_target_properties(lzmalib PROPERTIES PREFIX "")

install(TARGETS 7zDec DESTINATION bin)
install(TARGETS lzmasdk lzmalib DESTINATION lib)
install(FILES
    src/C/7z.h
    src/C/7zAlloc.h
    src/C/7zBuf.h
    src/C/7zCrc.h
    src/C/7zFile.h
    src/C/7zTypes.h
    src/C/7zVersion.h
    src/C/Aes.h
    src/C/Alloc.h
    src/C/Bcj2.h
    src/C/Bra.h
    src/C/Compiler.h
    src/C/CpuArch.h
    src/C/Delta.h
    src/C/DllSecur.h
    src/C/LzFind.h
    src/C/LzFindMt.h
    src/C/LzHash.h
    src/C/Lzma2Dec.h
    src/C/Lzma2DecMt.h
    src/C/Lzma2Enc.h
    src/C/Lzma86.h
    src/C/LzmaDec.h
    src/C/LzmaEnc.h
    src/C/LzmaLib.h
    src/C/MtCoder.h
    src/C/MtDec.h
    src/C/Ppmd.h
    src/C/Ppmd7.h
    src/C/Precomp.h
    src/C/RotateDefs.h
    src/C/Sha256.h
    src/C/Sort.h
    src/C/Threads.h
    src/C/Xz.h
    src/C/XzCrc64.h
    src/C/XzEnc.h
	src/C/ThreadsP.h
    DESTINATION include/lzmasdk)

##############################################################################

