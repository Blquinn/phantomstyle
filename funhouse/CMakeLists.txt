cmake_minimum_required(VERSION 3.16)
project(funhouse VERSION 1.0 LANGUAGES C CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Set up AUTOMOC and some sensible defaults for runtime execution
# When using Qt 6.3, you can replace the code block below with
# qt_standard_project_setup()
set(CMAKE_AUTOMOC ON)
include(GNUInstallDirs)

find_package(QT NAMES Qt5 Qt6 REQUIRED COMPONENTS Core)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Gui Widgets)

if (${QT_VERSION_MAJOR} GREATER_EQUAL 6)
qt_add_executable(funhouse WIN32 MACOSX_BUNDLE
    ../src/phantom/phantomcolor.cpp ../src/phantom/phantomcolor.h
    ../src/phantom/phantomstyle.cpp ../src/phantom/phantomstyle.h
    ../src/phantom/phantomtweak.h
    ColorTest.cpp
    FunHouse.cpp
    MainWindow.cpp
    PhTestApp.cpp PhTestApp.h
    Profiling.h
    WidgetGallery.cpp WidgetGallery.h
    main.cpp
)
else()
add_executable(funhouse WIN32 MACOSX_BUNDLE
    ../src/phantom/phantomcolor.cpp ../src/phantom/phantomcolor.h
    ../src/phantom/phantomstyle.cpp ../src/phantom/phantomstyle.h
    ../src/phantom/phantomtweak.h
    ColorTest.cpp
    FunHouse.cpp
    MainWindow.cpp
    PhTestApp.cpp PhTestApp.h
    Profiling.h
    WidgetGallery.cpp WidgetGallery.h
    main.cpp
)
endif()


target_include_directories(funhouse PRIVATE
    ../src/phantom
)

target_compile_definitions(funhouse PRIVATE
    QT_DEPRECATED_WARNINGS
)

target_link_libraries(funhouse PRIVATE
    Qt::Core
    Qt::Gui
    Qt::Widgets
)

if(EASY_PROFILE)
    target_include_directories(funhouse PRIVATE
        thirdparty/easy_profiler/easy_profiler_core/include
    )

    target_compile_definitions(funhouse PRIVATE
        BUILD_WITH_EASY_PROFILER
    )
endif()

if(g++)
    target_compile_options(funhouse
        -Wno-unknown-pragmas
        -Wno-unused-function
        -std=c++14
    )
endif()

if(MSVC)
    target_compile_options(funhouse
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
    target_compile_options(funhouse
        /wd4702
    )
endif()

if(DEFINES___contains___BUILD_WITH_EASY_PROFILER AND MSVC AND NOT CMAKE_BUILD_TYPE STREQUAL Debug)
    target_link_libraries(funhouse PRIVATE
        # Remove: L"${CMAKE_CURRENT_SOURCE_DIR}/thirdparty/easy_profiler/bin"
        easy_profiler
    )
endif()

install(TARGETS funhouse
    BUNDLE DESTINATION .
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

# Consider using qt_generate_deploy_app_script() for app deployment if
# the project can use Qt 6.3. In that case rerun qmake2cmake with
# --min-qt-version=6.3.
