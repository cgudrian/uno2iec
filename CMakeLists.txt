cmake_minimum_required(VERSION 3.16)

project(rpi2iec VERSION 0.1 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets SerialPort)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets SerialPort)

set(PROJECT_SOURCES
        main.cpp
        mainwindow.cpp mainwindow.hpp
        aboutdialog.cpp aboutdialog.hpp
        d64driver.cpp d64driver.hpp
        dirlistthemingconsts.hpp
        doscommands.cpp doscommands.hpp
        filedriverbase.cpp filedriverbase.hpp
        interface.cpp interface.hpp
        logfiltersetup.cpp logfiltersetup.hpp
        logger.cpp logger.hpp
        m2idriver.cpp m2idriver.hpp
        mountspecificfile.cpp mountspecificfile.h
        nativefs.cpp nativefs.hpp
        qcmdtextedit.cpp qcmdtextedit.h
        settingsdialog.cpp settingsdialog.hpp
        t64driver.cpp t64driver.hpp
        uno2iec/cbmdefines.h
        utils.hpp
        version.h
        x00fs.cpp x00fs.hpp
        x64driver.cpp x64driver.hpp

        mainwindow.ui
        aboutdialog.ui
        logfiltersetup.ui
        mountspecificfile.ui
        settingsdialog.ui

        resources.qrc
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)

    qt_add_executable(rpi2iec
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
# Define target properties for Android with Qt 6 as:
#    set_property(TARGET rpi2iec APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
else()
    if(ANDROID)
        add_library(rpi2iec SHARED
            ${PROJECT_SOURCES}
        )
# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(rpi2iec
            ${PROJECT_SOURCES}
        )
    endif()
endif()

target_compile_definitions(rpi2iec PRIVATE
    QT_IMPLICIT_QCHAR_CONSTRUCTION
)

target_include_directories(rpi2iec PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
)

target_link_libraries(rpi2iec PRIVATE
    Qt${QT_VERSION_MAJOR}::Widgets
    Qt${QT_VERSION_MAJOR}::SerialPort
)

# Qt for iOS sets MACOSX_BUNDLE_GUI_IDENTIFIER automatically since Qt 6.1.
# If you are developing for iOS or macOS you should consider setting an
# explicit, fixed bundle identifier manually though.
if(${QT_VERSION} VERSION_LESS 6.1.0)
  set(BUNDLE_ID_OPTION MACOSX_BUNDLE_GUI_IDENTIFIER com.example.rpi2iec)
endif()
set_target_properties(rpi2iec PROPERTIES
    ${BUNDLE_ID_OPTION}
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

include(GNUInstallDirs)
install(TARGETS rpi2iec
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(rpi2iec)
endif()
