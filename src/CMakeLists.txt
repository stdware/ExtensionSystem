set(EXTENSIONSYSTEM_PROJECT_DESCRIPTION "Qt Creator 3.6")
set(EXTENSIONSYSTEM_PROJECT_COPYRIGHT "Copyright The Qt Company Ltd.")

# ----------------------------------
# CMake API
# ----------------------------------
macro(extensionsystem_add_library _target)
    set(options AUTOGEN)
    set(oneValueArgs)
    set(multiValueArgs EXPORT_OPTIONS)
    cmake_parse_arguments(FUNC "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})

    if(FUNC_AUTOGEN)
        set(CMAKE_AUTOMOC ON)
        set(CMAKE_AUTOUIC ON)
        set(CMAKE_AUTORCC ON)
    endif()

    if(EXTENSIONSYSTEM_BUILD_STATIC)
        set(_type STATIC)
    else()
        set(_type SHARED)
    endif()

    add_library(${_target} ${_type})

    if(WIN32 AND(${_type} STREQUAL "SHARED"))
        qm_add_win_rc(${_target}
            NAME ${EXTENSIONSYSTEM_INSTALL_NAME}
            DESCRIPTION ${EXTENSIONSYSTEM_PROJECT_DESCRIPTION}
            COPYRIGHT ${EXTENSIONSYSTEM_PROJECT_COPYRIGHT}
        )
    endif()

    # Set global definitions
    qm_export_defines(${_target} ${FUNC_EXPORT_OPTIONS})

    # Configure target
    qm_configure_target(${_target} ${FUNC_UNPARSED_ARGUMENTS})

    # Add include directories
    target_include_directories(${_target} PRIVATE .)

    target_include_directories(${_target} PUBLIC
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/..>"
    )

    add_library(${EXTENSIONSYSTEM_INSTALL_NAME}::${_target} ALIAS ${_target})

    if(EXTENSIONSYSTEM_INSTALL)
        install(TARGETS ${_target}
            EXPORT ${EXTENSIONSYSTEM_INSTALL_NAME}Targets
            RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}" OPTIONAL
            LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}" OPTIONAL
            ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}" OPTIONAL
        )

        target_include_directories(${_target} PUBLIC
            "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/${EXTENSIONSYSTEM_INSTALL_NAME}>"
        )

        # Install headers
        get_filename_component(_dir_name ${CMAKE_CURRENT_LIST_DIR} NAME)
        file(GLOB _headers *.h)
        install(FILES ${_headers}
            DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${EXTENSIONSYSTEM_INSTALL_NAME}/${_dir_name}
        )
    endif()

    set_target_properties(${_target} PROPERTIES
        OUTPUT_NAME ${_target}36
    )
    target_compile_definitions(${_target} PRIVATE _UNICODE UNICODE)
endmacro()

# ----------------------------------
# Main Project
# ----------------------------------
add_subdirectory(aggregation)

add_subdirectory(utils)

add_subdirectory(extensionsystem)

# ----------------------------------
# Install
# ----------------------------------
if(EXTENSIONSYSTEM_INSTALL)
    # Add install target
    set(_install_dir ${CMAKE_INSTALL_LIBDIR}/cmake/${EXTENSIONSYSTEM_INSTALL_NAME})

    # Add version file
    write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/${EXTENSIONSYSTEM_INSTALL_NAME}ConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY AnyNewerVersion
    )

    # Add configuration file
    configure_package_config_file(
        ${CMAKE_CURRENT_LIST_DIR}/${EXTENSIONSYSTEM_INSTALL_NAME}Config.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/${EXTENSIONSYSTEM_INSTALL_NAME}Config.cmake"
        INSTALL_DESTINATION ${_install_dir}
        NO_CHECK_REQUIRED_COMPONENTS_MACRO
    )

    # Install cmake files
    install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/${EXTENSIONSYSTEM_INSTALL_NAME}Config.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/${EXTENSIONSYSTEM_INSTALL_NAME}ConfigVersion.cmake"
        DESTINATION ${_install_dir}
    )

    # Install cmake targets files
    install(EXPORT ${EXTENSIONSYSTEM_INSTALL_NAME}Targets
        FILE "${EXTENSIONSYSTEM_INSTALL_NAME}Targets.cmake"
        NAMESPACE ${EXTENSIONSYSTEM_INSTALL_NAME}::
        DESTINATION ${_install_dir}
    )
endif()