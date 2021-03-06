##############################################################################
# CMake
##############################################################################

cmake_minimum_required(VERSION 2.8.0)
project(motorosudp)
add_compile_options(-std=c++11)
##############################################################################
# Catkin
##############################################################################

# Lib RealSense

set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
endif()


find_package(realsense2 REQUIRED )

# Lib Open CV

SET("OpenCV_DIR" "/home/OpenCV/installation/OpenCV-3.4.4/share/OpenCV")
find_package(OpenCV REQUIRED )
# qt_build provides the qt cmake glue, roscpp the comms for a default talker
find_package(catkin REQUIRED COMPONENTS qt_build roscpp moveit_visual_tools interactive_markers visualization_msgs serial)
include_directories(${catkin_INCLUDE_DIRS})
# Use this to define what the package will export (e.g. libs, headers).
# Since the default here is to produce only a binary, we don't worry about
# exporting anything. 
catkin_package(CATKIN_DEPENDS moveit_visual_tools roscpp interactive_markers visualization_msgs serial)

##############################################################################
# Qt Environment
##############################################################################

# this comes from qt_build's qt-ros.cmake which is automatically 
# included via the dependency call in package.xml
rosbuild_prepare_qt4(QtCore QtGui QtNetwork) # Add the appropriate components to the component list here

##############################################################################
# Sections
##############################################################################

file(GLOB QT_FORMS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ui/*.ui)
file(GLOB QT_RESOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} resources/*.qrc)
file(GLOB_RECURSE QT_MOC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS include/motorosudp/*.hpp include/motorosudp/*.h ui/*.h)

QT4_ADD_RESOURCES(QT_RESOURCES_CPP ${QT_RESOURCES})
QT4_WRAP_UI(QT_FORMS_HPP ${QT_FORMS})
QT4_WRAP_CPP(QT_MOC_HPP ${QT_MOC})

##############################################################################
# Sources
##############################################################################

file(GLOB_RECURSE QT_SOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS src/*.cpp ui/*.cpp)

##############################################################################
# Binaries
##############################################################################

INCLUDE_DIRECTORIES( ${OpenCV_INCLUDE_DIRS} ${realsense_INCLUDE_DIR})
add_executable(motorosudp ${QT_SOURCES} ${QT_RESOURCES_CPP} ${QT_FORMS_HPP} ${QT_MOC_HPP})
target_link_libraries(motorosudp ${QT_LIBRARIES} ${catkin_LIBRARIES} ${OpenCV_LIBS} ${realsense2_LIBRARY})
install(TARGETS motorosudp RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})



