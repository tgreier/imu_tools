cmake_minimum_required(VERSION 3.5.1)
cmake_policy(SET CMP0048 NEW)
project(imu_filter_madgwick)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(sensor_msgs REQUIRED)

include_directories(include)

add_library(imu_filter_madgwick SHARED
            src/imu_filter.cpp
            src/imu_filter_ros.cpp
            src/stateless_orientation.cpp)
ament_target_dependencies(imu_filter_madgwick
  rclcpp
  rclcpp_components
  std_msgs
  sensor_msgs
  geometry_msgs
  tf2_geometry_msgs
  tf2_ros
)
rclcpp_components_register_nodes(imu_filter_madgwick "ImuFilterMadgwickRos")

add_executable(imu_filter_madgwick_node src/imu_filter_node.cpp)
target_link_libraries(imu_filter_madgwick_node imu_filter_madgwick)
ament_target_dependencies(imu_filter_madgwick_node
  rclcpp)

install(TARGETS
  imu_filter_madgwick
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

install(TARGETS
  imu_filter_madgwick_node
  DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY
  launch config
  DESTINATION share/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_cmake_gtest REQUIRED)
  add_subdirectory(test)
endif()

ament_export_include_directories(include)
ament_export_libraries(imu_filter_madgwick)

ament_package()
