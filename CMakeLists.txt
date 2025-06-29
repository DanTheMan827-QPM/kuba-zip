cmake_minimum_required(VERSION 3.22)

option(QUEST "Build for quest" ON)

# Globals
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED 20)
set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)

set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/zip/src)
set(INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/shared/zip)

add_compile_options(-frtti -fexceptions -fvisibility=hidden -fPIE -fPIC -Wno-invalid-offsetof -Werror=nonportable-include-path -lz)

# Include. Include order matters!
include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/utils.cmake)
include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/qpm.cmake)

if(${CMAKE_BUILD_TYPE} STREQUAL "DEBUG" OR ${CMAKE_BUILD_TYPE} STREQUAL "RelWithDebInfo")
        add_compile_options(-g)
endif()

# Targets
if(QUEST)
        include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/targets/quest.cmake)
endif()

# stop symbols leaking
# TODO: Fix
# add_link_options(-Wl, --exclude-libs, ALL)
project( zip
        VERSION ${PACKAGE_VERSION})

add_compile_definitions(MOD_ID="${CMAKE_PROJECT_NAME}")
add_compile_definitions(VERSION="${CMAKE_PROJECT_VERSION}")

# Set COMPILE_ID for qpm purposes
set(COMPILE_ID ${CMAKE_PROJECT_NAME})

# recursively get all src files
RECURSE_FILES(cpp_file_list ${SOURCE_DIR}/*.cpp)
RECURSE_FILES(c_file_list ${SOURCE_DIR}/*.c)

add_library(
        ${CMAKE_PROJECT_NAME}
        STATIC
        ${cpp_file_list}
        ${c_file_list}
)

# for inline hook
target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE -llog)

target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE ${INCLUDE_DIR})
