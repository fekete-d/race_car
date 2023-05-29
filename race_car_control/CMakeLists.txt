cmake_minimum_required(VERSION 3.0.2)
project(race_car_control)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  ackermann_steering_controller
  controller_manager
  joint_state_controller
  race_car_description
  robot_state_publisher
  roslaunch
  # ros_control
  # ros_controllers
  # steer_bot_hardware_gazebo
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
#  LIBRARIES race_car_control
#  CATKIN_DEPENDS ackermann_steering_controller controller_manager joint_state_controller race_car_description robot_state_publisher roslaunch steer_bot_hardware_gazebo
#  DEPENDS system_lib
)

roslaunch_add_file_check(launch)

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
  DIRECTORY config launch  
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)