cmake_minimum_required(VERSION 3.20)

project(CppSnake)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

file(GLOB SOURCES "*.cpp" "*.h" "*.ui")

set(Qt5_DIR "G:\\Virtuoso3D\\build\\vcpkg_installed\\v3d-x64-windows\\share\\cmake\\Qt5")
find_package(Qt5 REQUIRED COMPONENTS Core Gui Widgets REQUIRED)

add_executable(${PROJECT_NAME} ${SOURCES})

target_link_libraries(${PROJECT_NAME} PUBLIC Qt5::Core Qt5::Gui Qt5::Widgets)

# 添加以下内容来部署 Qt DLL
if(WIN32)
    get_target_property(_qmake_executable Qt5::qmake IMPORTED_LOCATION)
    get_filename_component(_qt_bin_dir "${_qmake_executable}" DIRECTORY)
    find_program(WINDEPLOYQT_EXECUTABLE windeployqt HINTS "${_qt_bin_dir}")

    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND "${CMAKE_COMMAND}" -E env PATH="${_qt_bin_dir}" "${WINDEPLOYQT_EXECUTABLE}"
            --verbose 0
            --no-compiler-runtime
            --no-angle
            --no-opengl-sw
            \"$<TARGET_FILE:${PROJECT_NAME}>\"
        COMMENT "Running windeployqt..."
    )
endif()