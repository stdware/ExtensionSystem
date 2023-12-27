project(Aggregation VERSION ${EXTENSIONSYSTEM_VERSION} LANGUAGES CXX)

file(GLOB _src *.h *.cpp)

extensionsystem_add_library(${PROJECT_NAME} AUTOGEN
    SOURCES ${_src}
    QT_LINKS Core
)