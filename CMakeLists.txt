cmake_minimum_required(VERSION 3.20)
project(polyguns-engine LANGUAGES C CXX)

# ----------------------------------------------------------------------------
# Language settings
# ----------------------------------------------------------------------------
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Export compile_commands.json for tooling (clangd, etc.)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Build type" FORCE)
endif()

# Enable CTest. Picks up any tests added under tests/ via add_test().
# Even without tests this makes the `ctest` task run cleanly.
enable_testing()

# ----------------------------------------------------------------------------
# Third-party dependencies
#
# We pull GLFW (windowing/input) and GLAD 2 (OpenGL function loader) via
# CMake's FetchContent. Sources are downloaded into polyguns-engine/third_party
# at configure time. The downloaded directories are gitignored.
# ----------------------------------------------------------------------------
set(FETCHCONTENT_BASE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/third_party"
    CACHE PATH "Where FetchContent stores its sources/builds" FORCE)

include(FetchContent)

# --- GLFW --------------------------------------------------------------------
set(GLFW_BUILD_DOCS     OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS    OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL        OFF CACHE BOOL "" FORCE)

FetchContent_Declare(
    glfw
    GIT_REPOSITORY https://github.com/glfw/glfw.git
    GIT_TAG        3.4
    GIT_SHALLOW    TRUE
)
FetchContent_MakeAvailable(glfw)

# --- GLAD 2 ------------------------------------------------------------------
# glad2 ships a CMake helper (glad_add_library) that generates the loader
# source on the fly. Requires Python 3 + Jinja2 at configure time.
FetchContent_Declare(
    glad
    GIT_REPOSITORY https://github.com/Dav1dde/glad.git
    GIT_TAG        v2.0.6
    GIT_SHALLOW    TRUE
    SOURCE_SUBDIR  cmake
)
FetchContent_MakeAvailable(glad)

# Generate a static GLAD library targeting OpenGL 3.3 core.
glad_add_library(glad_gl_core_33 STATIC API gl:core=3.3)

# ----------------------------------------------------------------------------
# Engine target
# ----------------------------------------------------------------------------
add_executable(polyguns-engine
    src/main.cpp
)

target_include_directories(polyguns-engine
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_link_libraries(polyguns-engine
    PRIVATE
        glfw
        glad_gl_core_33
)

# Warnings
if(MSVC)
    target_compile_options(polyguns-engine PRIVATE /W4 /permissive-)
else()
    target_compile_options(polyguns-engine PRIVATE -Wall -Wextra -Wpedantic)
endif()
