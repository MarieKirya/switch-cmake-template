cmake_minimum_required (VERSION 2.6)
project (SwitchTemplate)

if(NOT DEFINED ENV{DEVKITPRO})
    message( FATAL_ERROR "Please set DEVKITPRO in your environment. export DEVKITPRO=<path to>/devkitpro")
endif()

set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR aarch64)

set(SWITCH_LIBNX TRUE)

set(DEVKITPRO "$ENV{DEVKITPRO}")
set(LIBNX "${DEVKITPRO}/libnx")
set(PORTLIBS_PATH "${DEVKITPRO}/portlibs")
set(PORTLIBS "${PORTLIBS_PATH}/switch")

# Packaging info start.
set(TARGET "switch-template")

set(APP_VERSION_MAJOR 1)
set(APP_VERSION_MINOR 0)
set(APP_VERSION_PATCH 0)

set(APP_TITLE "Switch Template")
set(APP_AUTHOR "Unspecified Author")
set(APP_VERSION "${APP_VERSION_MAJOR}.${APP_VERSION_MINOR}.${APP_VERSION_PATCH}")
set(APP_ICON "${LIBNX}/default_icon.jpg")

set(BUILD_EXEFS_SRC "exefs_src")
# Packaging info end. 

# Build packaging script with info.
configure_file(
	"${PROJECT_BINARY_DIR}/package_switch.sh.in"
	"${PROJECT_BINARY_DIR}/package_switch.sh"
)

set(ARCH "-march=armv8-a -mtune=cortex-a57 -mtp=soft -fPIE")
set(NX_C_COMPILE_FLAGS "-g -Wall -O2 -ffunction-sections -D__SWITCH__ ${ARCH}")
set(NX_CXX_COMPILE_FLAGS "${NX_C_COMPILE_FLAGS} -std=c++17 -fno-rtti -fno-exceptions")
set(NX_LINK_FLAGS "-specs=${LIBNX}/switch.specs -g ${ARCH} -Wl,-Map,${TARGET}.map")

# Prevent CMake from assuming we're compiling for macOS and adding platform specific flags
# that strip symbols from resulting elf files.
if ( APPLE )
	string ( REPLACE "-Wl,-search_paths_first" "" CMAKE_C_LINK_FLAGS ${CMAKE_C_LINK_FLAGS} )
	string ( REPLACE "-Wl,-search_paths_first" "" CMAKE_CXX_LINK_FLAGS ${CMAKE_CXX_LINK_FLAGS} )
	string ( REPLACE "-Wl,-headerpad_max_install_names" "" CMAKE_C_LINK_FLAGS ${CMAKE_C_LINK_FLAGS} )
	string ( REPLACE "-Wl,-headerpad_max_install_names" "" CMAKE_CXX_LINK_FLAGS ${CMAKE_CXX_LINK_FLAGS} )
endif ()

set(CMAKE_C_COMPILE_FLAGS "${CMAKE_C_COMPILE_FLAGS} ${NX_C_COMPILE_FLAGS}")
set(CMAKE_CXX_COMPILE_FLAGS  "${CMAKE_CXX_COMPILE_FLAGS} ${NX_CXX_COMPILE_FLAGS}")
set(CMAKE_C_LINK_FLAGS  "${CMAKE_C_LINK_FLAGS} ${NX_LINK_FLAGS}")
set(CMAKE_CXX_LINK_FLAGS  "${CMAKE_CXX_LINK_FLAGS} ${NX_LINK_FLAGS}")

LIST(APPEND CMAKE_PROGRAM_PATH  "${DEVKITPRO}/tools/bin")
LIST(APPEND CMAKE_PROGRAM_PATH  "${DEVKITPRO}/devkitA64/bin")

include_directories("${PROJECT_BINARY_DIR}")
include_directories("${PORTLIBS}/include")
include_directories("${LIBNX}/include")

link_directories("${LIBNX}/lib")
link_directories("${PORTLIBS}/lib")

add_executable("${TARGET}.elf" source/main.c)
target_link_libraries("${TARGET}.elf" nx)

add_executable("${TARGET}-cpp.elf" source/main.cpp)
target_link_libraries("${TARGET}-cpp.elf" nx)