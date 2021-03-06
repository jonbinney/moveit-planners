cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
set(ROS_BUILD_TYPE Release)

rosbuild_init()

rosbuild_add_boost_directories()

find_package(Eigen REQUIRED)
include_directories(${EIGEN_INCLUDE_DIRS})


#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#uncomment if you have defined messages
#rosbuild_genmsg()
#uncomment if you have defined services
#rosbuild_gensrv()

#common commands for building c++ executables and libraries
rosbuild_add_library(${PROJECT_NAME} src/ompl_interface_ros.cpp)


rosbuild_add_executable(ompl_planner src/ompl_planner.cpp)
target_link_libraries(ompl_planner ${PROJECT_NAME})

set(SOURCE_FILES src/ompl_plugin.cpp)
set(LIBRARY_NAME ompl_planner_plugin)
include(CMakeDetermineSystem)
if(CMAKE_SYSTEM_NAME MATCHES "Darwin")
  rosbuild_add_library_module(${LIBRARY_NAME} ${SOURCE_FILES})
else(CMAKE_SYSTEM_NAME MATCHES "Darwin")
  rosbuild_add_library(${LIBRARY_NAME} ${SOURCE_FILES})
endif(CMAKE_SYSTEM_NAME MATCHES "Darwin")
target_link_libraries(${LIBRARY_NAME} ${PROJECT_NAME})

#add dynamic reconfigure api
rosbuild_find_ros_package(dynamic_reconfigure)
include(${dynamic_reconfigure_PACKAGE_PATH}/cmake/cfgbuild.cmake)
gencfg()
