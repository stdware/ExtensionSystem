cmake_minimum_required(VERSION 3.17)

project(ExtensionSystem VERSION 0.3.6.1 LANGUAGES CXX)

# ----------------------------------
# Build Options
# ----------------------------------
option(EXTENSIONSYSTEM_BUILD_STATIC "Build static libraries" OFF)
option(EXTENSIONSYSTEM_INSTALL "Install library" ON)

option(EXTENSIONSYSTEM_ENABLE_QML "" off)
option(EXTENSIONSYSTEM_NO_FILENAME_LIST "" on)

# ----------------------------------
# CMake Settings
# ----------------------------------
if(MSVC)
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /manifest:no")
    set(CMAKE_MODULE_LINKER_FLAGS "${CMAKE_MODULE_LINKER_FLAGS} /manifest:no")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /manifest:no")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /utf-8")

    if(NOT DEFINED CMAKE_DEBUG_POSTFIX)
        set(CMAKE_DEBUG_POSTFIX "d")
    endif()
elseif(MINGW)
    set(CMAKE_STATIC_LIBRARY_PREFIX "")
    set(CMAKE_SHARED_LIBRARY_PREFIX "")
endif()

if(EXTENSIONSYSTEM_INSTALL)
    include(GNUInstallDirs)
    include(CMakePackageConfigHelpers)
endif()

# ----------------------------------
# Project Variables
# ----------------------------------
set(EXTENSIONSYSTEM_VERSION ${PROJECT_VERSION})
set(EXTENSIONSYSTEM_INSTALL_NAME ${PROJECT_NAME})

# ----------------------------------
# Find basic dependencies
# ----------------------------------
find_package(qmsetup REQUIRED)

qm_import(Filesystem)
qm_init_directories()

# ----------------------------------
# Add source modules
# ----------------------------------
add_subdirectory(src)
