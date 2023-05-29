cmake_minimum_required(VERSION 3.0.2)
project(race_car_gazebo)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  controller_manager
  race_car_description 
  race_car_control 
  gazebo_ros
  gazebo_ros_control
  joint_state_controller
  robot_state_publisher
  rqt_robot_steering
)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if your package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES race_car_gazebo
  CATKIN_DEPENDS 
    controller_manager 
    race_car_description 
    race_car_control 
    gazebo_ros 
    gazebo_ros_control 
    joint_state_controller 
    robot_state_publisher 
    rqt_robot_steering
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
# include
  ${catkin_INCLUDE_DIRS}
)


#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html


# Mark other files for installation (e.g. launch and bag files, etc.)
install(
  DIRECTORY launch models worlds
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
