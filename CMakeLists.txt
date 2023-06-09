cmake_minimum_required(VERSION 3.5)

project(QiPT VERSION 0.1 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 REQUIRED COMPONENTS Widgets Network WebEngineWidgets WebEngineCore)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets Network WebEngineWidgets WebEngineCore)

set(PROJECT_SOURCES
        main.cpp
        UI/mainwindow.cpp
        UI/mainwindow.h
        UI/InputDialog.h
        UI/InputDialog.cpp
        UI/ChatWidget.h
        UI/ChatWidget.cpp
        UI/ImageWidget.h
        UI/ImageWidget.cpp
        OpenAIClient/OpenAIClientFactory.h
        OpenAIClient/OpenAIClientFactory.cpp
        OpenAIClient/OpenAIApiClient.h
        OpenAIClient/OpenAIApiClient.cpp
        OpenAIClient/OpenAIChatClient.h
        OpenAIClient/OpenAIChatClient.cpp
        OpenAIClient/OpenAIImageClient.h
        OpenAIClient/OpenAIImageClient.cpp
        FileSystem/ChatLogger.h
        FileSystem/ChatLogger.cpp
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(QiPT
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
# Define target properties for Android with Qt 6 as:
#    set_property(TARGET QiPT APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
else()
    if(ANDROID)
        add_library(QiPT SHARED
            ${PROJECT_SOURCES}
        )
# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(QiPT
            ${PROJECT_SOURCES}
        )
    endif()
endif()

target_link_libraries(QiPT PRIVATE Qt${QT_VERSION_MAJOR}::Widgets Qt${QT_VERSION_MAJOR}::Network Qt${QT_VERSION_MAJOR}::WebEngineWidgets Qt${QT_VERSION_MAJOR}::WebEngineCore)

set_target_properties(QiPT PROPERTIES
    MACOSX_BUNDLE_GUI_IDENTIFIER my.example.com
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

include_directories(${CMAKE_SOURCE_DIR})

install(TARGETS QiPT
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(QiPT)
endif()
