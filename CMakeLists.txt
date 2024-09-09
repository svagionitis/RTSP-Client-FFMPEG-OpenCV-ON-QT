cmake_minimum_required(VERSION 3.5)

project(RtspClient VERSION 0.0.1 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Enable the compile_commands.json
# See https://cmake.org/cmake/help/latest/variable/CMAKE_EXPORT_COMPILE_COMMANDS.html
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(OpenCV_MIN_VERSION "4.2.0")
find_package(OpenCV ${OpenCV_MIN_VERSION} REQUIRED)
message(STATUS "OpenCV libs: ${OpenCV_LIBS}")

find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBAV REQUIRED IMPORTED_TARGET
    libavcodec
    libavfilter
    libavdevice
    libavformat
    libswresample
    libswscale
    libavutil
)

include_directories( ${OpenCV_INCLUDE_DIRS} )

set(TARGET_LINK_LIBRARIES
    PkgConfig::LIBAV
    ${OpenCV_LIBS}
)

set(PROJECT_SOURCES
    ${CMAKE_SOURCE_DIR}/ffmpegdecoder.h
    ${CMAKE_SOURCE_DIR}/ffmpegdecoder.cpp
    ${CMAKE_SOURCE_DIR}/main.cpp
)

# If the user specifies -DCMAKE_BUILD_TYPE on the command line, take their
# definition and dump it in the cache along with proper documentation,
# otherwise set CMAKE_BUILD_TYPE to Debug prior to calling PROJECT()
#
# See https://cmake.org/pipermail/cmake/2008-September/023808.html
#     https://cmake.org/pipermail/cmake/2009-June/030311.html
# License: BSD 3-clause
# Author: Philip Lowman
#
if (CMAKE_BUILD_TYPE)
   set(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE
       STRING "Choose the type of build, options are:
- None (CMAKE_CXX_FLAGS)
- Debug
- Release
- RelWithDebInfo
- MinSizeRel.")
else ()
   set(CMAKE_BUILD_TYPE Debug CACHE
       STRING "Choose the type of build, options are:
- None (CMAKE_CXX_FLAGS)
- Debug
- Release
- RelWithDebInfo
- MinSizeRel."
       FORCE)
endif ()

add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})

target_link_libraries(${PROJECT_NAME} PRIVATE ${TARGET_LINK_LIBRARIES})
target_include_directories(${PROJECT_NAME} PUBLIC ${TARGET_INCLUDE_DIRECTORIES})

install(TARGETS ${PROJECT_NAME}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
