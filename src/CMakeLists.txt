cmake_minimum_required(VERSION 3.16)
project(phantomstyleplugin VERSION 1.0 LANGUAGES C CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Set up AUTOMOC and some sensible defaults for runtime execution
# When using Qt 6.3, you can replace the code block below with
# qt_standard_project_setup()
set(CMAKE_AUTOMOC ON)
include(GNUInstallDirs)

find_package(QT NAMES Qt5 Qt6 REQUIRED COMPONENTS Core)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Gui Widgets)

if (${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_plugin(phantomstyleplugin)
else()
    add_library(phantomstyleplugin SHARED)
endif()

target_sources(phantomstyleplugin PRIVATE
    phantom/phantomcolor.cpp
    phantom/phantomstyle.cpp
    styleplugin/phantomstyleplugin.cpp
)

# set_target_properties(phantomstyleplugin PROPERTIES
#     SOVERSION 1
#     PUBLIC_HEADER include/phantom.h
# )

target_include_directories(phantomstyleplugin PUBLIC
    phantom
    styleplugin
)

configure_file(phantomstyleplugin.pc.in phantomstyleplugin.pc @ONLY)

target_compile_definitions(phantomstyleplugin PRIVATE
    QT_DEPRECATED_WARNINGS
)

target_link_libraries(phantomstyleplugin PRIVATE
    Qt::Core
    Qt::Gui
    Qt::Widgets
)

if(g++)
    target_compile_options(phantomstyleplugin
        -Wno-unknown-pragmas
        -Wno-unused-function
    )
endif()

if(MSVC)
    target_compile_options(phantomstyleplugin
        /Wall
        /w14062
        /wd4068
        /wd4365
        /wd4371
        /wd4548
        /wd4619
        /wd4623
        /wd4625
        /wd4626
        /wd4710
        /wd4711
        /wd4774
        /wd4820
        /wd4946
        /wd5026
        /wd5027
        /wd5045
    )
endif()

if(CMAKE_BUILD_TYPE STREQUAL Release AND MSVC)
    target_compile_options(phantomstyleplugin
        /wd4702
    )
endif()

install(TARGETS phantomstyleplugin
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    FRAMEWORK DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(FILES ${CMAKE_BINARY_DIR}/phantomstyleplugin.pc
    DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/pkgconfig)
