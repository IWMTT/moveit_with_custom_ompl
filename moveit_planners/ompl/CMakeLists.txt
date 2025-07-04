cmake_minimum_required(VERSION 3.1.3)
project(moveit_planners_ompl)

find_package(Boost REQUIRED system filesystem date_time thread serialization)
find_package(catkin REQUIRED COMPONENTS
  moveit_core
  moveit_ros_planning
  roscpp
  rosconsole
  pluginlib
  tf2
  dynamic_reconfigure
  # added
  std_msgs 
  message_generation
  # end of added
)
moveit_build_options()

find_package(ompl REQUIRED)
if(TARGET ompl::ompl)
  moveit_get_include_and_libs_from_target(OMPL ompl::ompl)
endif()

generate_dynamic_reconfigure_options("ompl_interface/cfg/OMPLDynamicReconfigure.cfg")


# added
add_service_files(
  FILES
  CustomCost.srv
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

# end of added

catkin_package(
  LIBRARIES
    moveit_ompl_interface
  INCLUDE_DIRS
    ompl_interface/include
  CATKIN_DEPENDS
    dynamic_reconfigure
    moveit_core
    roscpp
    #added
    std_msgs
    message_runtime
    #end of added
  DEPENDS
    OMPL
)


include_directories(ompl_interface/include)
include_directories(SYSTEM
                    ${catkin_INCLUDE_DIRS}
                    ${Boost_INCLUDE_DIRS}
                    ${OMPL_INCLUDE_DIRS})

add_subdirectory(ompl_interface)

install(FILES ompl_interface_plugin_description.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(DIRECTORY launch DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
