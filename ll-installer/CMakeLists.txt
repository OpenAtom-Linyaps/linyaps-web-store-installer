set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
add_definitions("-DQT_MESSAGELOGCONTEXT")

# we need support Qt 5 versions older than 5.15
# https://doc.qt.io/qt-6/cmake-qt5-and-qt6-compatibility.html#supporting-qt-5-versions-older-than-5-15
if(${QT_VERSION_MAJOR})
  message(STATUS "trying specified Qt version: ${QT_VERSION_MAJOR}")
  find_package(
    Qt${QT_VERSION_MAJOR}
    COMPONENTS Core Widgets
    REQUIRED)
else()
  message(STATUS "resolving Qt version automatically")

  while(true)
    find_package(Qt6 COMPONENTS Core Widgets)
    if(Qt6_FOUND)
      set(QT_VERSION_MAJOR "6")
      break()
    endif()

    find_package(Qt5 COMPONENTS Core Widgets)
    if(Qt5_FOUND)
      set(QT_VERSION_MAJOR "5")
      break()
    endif()

    message(FATAL_ERROR "Qt not found")
  endwhile()
endif()

message(STATUS "compiling with Qt${QT_VERSION_MAJOR}")

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)

add_executable(ll-installer installer-dialog.cpp main.cpp)

target_link_libraries(ll-installer PRIVATE Qt${QT_VERSION_MAJOR}::Core
                                           Qt${QT_VERSION_MAJOR}::Widgets)

install(TARGETS ll-installer RUNTIME DESTINATION bin)

install(FILES space.linglong.Installer.desktop
        DESTINATION ${CMAKE_INSTALL_PREFIX}/share/applications)
