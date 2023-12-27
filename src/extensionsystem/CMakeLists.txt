project(ExtensionSystem VERSION ${EXTENSIONSYSTEM_VERSION} LANGUAGES CXX)

file(GLOB _src *.h *.cpp *.ui *.qrc newSettings/*.h newSettings/*.cpp)

extensionsystem_add_library(${PROJECT_NAME} AUTOGEN
    SOURCES ${_src}
    LINKS Aggregation Utils
    QT_LINKS Core Widgets
    SKIP_AUTOMOC "pluginmanager.cpp"
)