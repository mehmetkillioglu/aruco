cmake_minimum_required(VERSION 3.5)
project(aruco)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()


#Enable C++ 11
add_compile_options(-std=c++11)

#Packages
find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(image_transport REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(builtin_interfaces REQUIRED)

find_package(OpenCV REQUIRED)

#Messages
set(msg_files
  "msg/Marker.msg"
)

rosidl_generate_interfaces(${PROJECT_NAME}
  ${msg_files}
  DEPENDENCIES builtin_interfaces std_msgs
)

ament_export_dependencies(rosidl_default_runtime)


#Aruco ROS node
add_executable(maruco src/ros/ArucoNode.cpp)
ament_target_dependencies(
	maruco
	"rclcpp"
	"cv_bridge"
	"std_msgs"
	"image_transport"
	"OpenCV"
)

get_default_rmw_implementation(rmw_implementation)
find_package("${rmw_implementation}" REQUIRED)
get_rmw_typesupport(typesupport_impls "${rmw_implementation}" LANGUAGE "cpp")

foreach(typesupport_impl ${typesupport_impls})
  rosidl_target_interfaces(maruco
    ${PROJECT_NAME} ${typesupport_impl}
  )
endforeach()


install(TARGETS
  maruco
  DESTINATION lib/${PROJECT_NAME})


ament_package()
