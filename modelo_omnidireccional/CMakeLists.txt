cmake_minimum_required(VERSION 3.5)
project(modelo_omnidireccional)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(robmovil_msgs REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)

# --- omni_odometry_node (Ejercicio 1) ---
add_executable(omni_odometry_node
  src/omni_odometry.cpp
  src/omni_odometry_node.cpp
)
ament_target_dependencies(omni_odometry_node
  rclcpp std_msgs geometry_msgs nav_msgs
  robmovil_msgs tf2 tf2_ros tf2_geometry_msgs
)

install(TARGETS
  omni_odometry_node
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()
# --- trajectory_pilot_node (Ejercicio 2) ---
add_executable(trajectory_pilot_node
  src/trajectory_generator.cpp
  src/trajectory_pilot.cpp
  src/trajectory_pilot_node.cpp
)
ament_target_dependencies(trajectory_pilot_node
  rclcpp std_msgs geometry_msgs nav_msgs
  robmovil_msgs tf2 tf2_ros tf2_geometry_msgs
)
install(TARGETS
  trajectory_pilot_node
  DESTINATION lib/${PROJECT_NAME}
)
