cmake_minimum_required(VERSION 2.8.3)
project(robotino_calibration)

## C++11
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
    add_definitions(-DCPP11SUPPORTED)
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
    add_definitions(-DCPP11SUPPORTED)
else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()
## End C++11

set(catkin_RUN_PACKAGES			# all ROS packages from package.xml (libopencv-dev is system dependency --> sudo apt-get install)
	cob_fiducials
	cob_object_detection_msgs
	control_msgs
	cv_bridge
	geometry_msgs
	image_transport
	roscpp
	roslib
	sensor_msgs
	std_msgs
	tf
	trajectory_msgs
)

set(catkin_BUILD_PACKAGES 
	${catkin_RUN_PACKAGES}
	cmake_modules
)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED
	${catkin_BUILD_PACKAGES}		# this makes ${catkin_LIBRARIES} include all libraries of ${catkin_BUILD_PACKAGES}
)

## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED COMPONENTS system filesystem)
find_package(OpenCV REQUIRED)	# name identical to FindOpenCV.cmake in cmake_modules
find_package(PCL REQUIRED)

###################################
## catkin specific configuration ##
###################################

catkin_package(
INCLUDE_DIRS
	common/include
	ros/include
LIBRARIES
CATKIN_DEPENDS
	${catkin_RUN_PACKAGES}
DEPENDS
	Boost
	OpenCV
	PCL
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
	common/include
	ros/include
	${catkin_INCLUDE_DIRS} 		# same name as find_package
	${Boost_INCLUDE_DIRS}
	${OpenCV_INCLUDE_DIRS}
	${PCL_INCLUDE_DIRS}
)

# calibration software
add_executable(camera_base_calibration	ros/src/camera_base_calibration_node.cpp
										ros/src/robot_calibration.cpp
										ros/src/calibration_interface.cpp
										ros/src/robotino_interface.cpp
										ros/src/raw_interface.cpp
										ros/src/cob_interface.cpp
										ros/src/camera_base_calibration_marker.cpp
										ros/src/camera_base_calibration_checkerboard.cpp
										ros/src/camera_base_calibration_pitag.cpp
										common/src/transformation_utilities.cpp
										common/src/calibration_utilities.cpp
)
target_link_libraries(camera_base_calibration
	${catkin_LIBRARIES} # automatically links all catkin_BUILD_PACKAGES
	${Boost_LIBRARIES}
	${OpenCV_LIBS}
	${PCL_LIBRARIES}
)
add_dependencies(camera_base_calibration	${catkin_EXPORTED_TARGETS})

add_executable(arm_base_calibration  ros/src/arm_base_calibration_node.cpp
										ros/src/robot_calibration.cpp
										ros/src/calibration_interface.cpp
										ros/src/robotino_interface.cpp
										ros/src/raw_interface.cpp
										ros/src/cob_interface.cpp
										ros/src/arm_base_calibration.cpp
										common/src/transformation_utilities.cpp
										common/src/calibration_utilities.cpp
)
target_link_libraries(arm_base_calibration
	${catkin_LIBRARIES} # automatically links all catkin_BUILD_PACKAGES
	${Boost_LIBRARIES}
	${OpenCV_LIBRARIES}
	${PCL_LIBRARIES}
)
add_dependencies(arm_base_calibration	${catkin_EXPORTED_TARGETS})


#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
install(TARGETS camera_base_calibration
	ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
	LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
	RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(TARGETS arm_base_calibration
	ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
	LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
	RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )
