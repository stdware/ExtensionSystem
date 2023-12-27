project(Utils VERSION ${EXTENSIONSYSTEM_VERSION} LANGUAGES CXX)

file(GLOB _src
    *.h *.cpp
    mimetypes/*.h mimetypes/*.cpp
    theme/*.h theme/*.cpp
    tooltip/*.h tooltip/*.cpp
)

if(WIN32)
    list(FILTER _src EXCLUDE REGEX "unixutils.*")
    set(_platform_libs iphlpapi ws2_32 user32 shell32)
else()
    list(FILTER _src EXCLUDE REGEX "winutils.*")
endif()

if(APPLE)
    list(APPEND _src fileutils_mac.mm)
    set(_platform_link_flags "-framework Foundation")
endif()

if(EXTENSIONSYSTEM_NO_FILENAME_LIST)
    set(_defines EXTENSIONSYSTEM_NO_FILENAME_LIST)
endif()

set(_qt_modules Core Widgets Network Concurrent)

if(EXTENSIONSYSTEM_ENABLE_QML)
    list(APPEND _src unused_src/templateengine.h unused_src/templateengine.cpp)
    list(APPEND _qt_modules Qml)
endif()

extensionsystem_add_library(${PROJECT_NAME} AUTOGEN
    SOURCES ${_src}
    LINKS ${_qt_libs}
    LINKS_PRIVATE ${_platform_link_flags} ${_platform_libs}
    QT_LINKS ${_qt_modules}
    DEFINES ${_defines}
    EXPORT_OPTIONS
        STATIC  QTCREATOR_UTILS_STATIC_LIB
        LIBRARY QTCREATOR_UTILS_LIB
)