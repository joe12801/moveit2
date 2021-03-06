cmake_minimum_required(VERSION 3.10.2)
project(moveit_ros_perception)

# Common cmake code applied to all moveit packages
find_package(moveit_common REQUIRED)
moveit_package()

option(WITH_OPENGL "Build the parts that depend on OpenGL" ON)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wpedantic)
endif()

find_package(Boost REQUIRED thread)

# if(WITH_OPENGL)
#   # Prefer newer vendor-specific OpenGL library
#   if(POLICY CMP0072)
#     cmake_policy(SET CMP0072 NEW)
#   endif()
#   find_package(OpenGL REQUIRED)
#   find_package(GLEW REQUIRED)
#   find_package(GLUT REQUIRED)
#   set(gl_LIBS ${gl_LIBS} ${OPENGL_LIBRARIES})
#   set(perception_GL_INCLUDE_DIRS "mesh_filter/include" "depth_image_octomap_updater/include")
#   set(SYSTEM_GL_INCLUDE_DIRS ${GLEW_INCLUDE_DIR} ${GLUT_INCLUDE_DIR})
# endif()

if(APPLE)
  find_package(X11 REQUIRED)
endif()

find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(moveit_core REQUIRED)
find_package(rclcpp REQUIRED)
find_package(urdf REQUIRED)
find_package(message_filters REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_eigen REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(pluginlib REQUIRED)
find_package(image_transport REQUIRED)
find_package(object_recognition_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(moveit_msgs REQUIRED)
find_package(moveit_ros_occupancy_map_monitor REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(OpenMP REQUIRED)
find_package(OpenCV)

set(THIS_PACKAGE_INCLUDE_DIRS
  lazy_free_space_updater/include
  point_containment_filter/include
  pointcloud_octomap_updater/include
  semantic_world/include
  # ${perception_GL_INCLUDE_DIRS}
)

set(THIS_PACKAGE_LIBRARIES
  moveit_lazy_free_space_updater
  moveit_point_containment_filter
  moveit_pointcloud_octomap_updater
  moveit_semantic_world
)

set(THIS_PACKAGE_INCLUDE_DEPENDS
  image_transport
  moveit_core
  moveit_msgs
  moveit_ros_occupancy_map_monitor
  object_recognition_msgs
  rclcpp
  sensor_msgs
  tf2_geometry_msgs
  Eigen3
)

include_directories(${THIS_PACKAGE_INCLUDE_DIRS})
include_directories(SYSTEM
  ${Boost_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${SYSTEM_GL_INCLUDE_DIR}
  ${X11_INCLUDE_DIR}
)

add_subdirectory(lazy_free_space_updater)
add_subdirectory(point_containment_filter)
add_subdirectory(pointcloud_octomap_updater)
# if(WITH_OPENGL)
#   add_subdirectory(mesh_filter)
#   add_subdirectory(depth_image_octomap_updater)
# endif()

add_subdirectory(semantic_world)

ament_export_include_directories(include)
ament_export_libraries(${THIS_PACKAGE_LIBRARIES})
ament_export_dependencies(${THIS_PACKAGE_INCLUDE_DEPENDS})

pluginlib_export_plugin_description_file(moveit_ros_occupancy_map_monitor "pointcloud_octomap_updater_plugin_description.xml")
# pluginlib_export_plugin_description_file(moveit_ros_occupancy_map_monitor "depth_image_octomap_updater_plugin_description.xml")

ament_package()
