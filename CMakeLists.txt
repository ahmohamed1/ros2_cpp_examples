cmake_minimum_required(VERSION 3.8)
project(ros2_cpp_examples)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(ament_cmake_python REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_action REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(alphabot_msgs REQUIRED)
find_package(rclpy REQUIRED)
find_package(std_msgs REQUIRED)
find_package(PkgConfig)
find_package(rclcpp_lifecycle REQUIRED)
find_package(hardware_interface REQUIRED)
find_package(pluginlib REQUIRED)

pkg_check_modules(SERIAL libserial)

add_executable(simple_publisher src/simple_publisher.cpp)
ament_target_dependencies(simple_publisher rclcpp std_msgs)

add_executable(simple_subscriber src/simple_subscriber.cpp)
ament_target_dependencies(simple_subscriber rclcpp std_msgs)

add_executable(simple_parameter src/simple_parameter.cpp)
ament_target_dependencies(simple_parameter rclcpp rcl_interfaces)




add_executable(simple_serial_receiver src/simple_serial_receiver.cpp)
ament_target_dependencies(simple_serial_receiver rclcpp std_msgs)
target_include_directories(simple_serial_receiver PUBLIC
 "${SERIAL_INCLUDE_DIRS}")
target_link_libraries(simple_serial_receiver
 "${SERIAL_LDFLAGS}")

add_executable(simple_serial_transmitter src/simple_serial_transmitter.cpp)
ament_target_dependencies(simple_serial_transmitter rclcpp std_msgs)
target_include_directories(simple_serial_transmitter PUBLIC
 "${SERIAL_INCLUDE_DIRS}")
target_link_libraries(simple_serial_transmitter
 "${SERIAL_LDFLAGS}")

add_executable(simple_qos_publisher src/simple_qos_publisher.cpp)
ament_target_dependencies(simple_qos_publisher rclcpp std_msgs)

add_executable(simple_qos_subscriber src/simple_qos_subscriber.cpp)
ament_target_dependencies(simple_qos_subscriber rclcpp std_msgs)

add_library(simple_action_server SHARED src/simple_action_server.cpp)
target_include_directories(simple_action_server PRIVATE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)
target_compile_definitions(simple_action_server
  PRIVATE "SIMPLE_ACTION_SERVER_CPP_BUILDING_DLL")
ament_target_dependencies(simple_action_server
  "alphabot_msgs"
  "rclcpp"
  "rclcpp_action"
  "rclcpp_components")
rclcpp_components_register_node(simple_action_server 
  PLUGIN "ros2_cpp_examples::SimpleActionServer" 
  EXECUTABLE simple_action_server_node
)

add_library(simple_action_client SHARED src/simple_action_client.cpp)
target_include_directories(simple_action_client PRIVATE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)
target_compile_definitions(simple_action_client
  PRIVATE "SIMPLE_ACTION_CLIENT_CPP_BUILDING_DLL")
ament_target_dependencies(simple_action_client
  "alphabot_msgs"
  "rclcpp"
  "rclcpp_action"
  "rclcpp_components")
rclcpp_components_register_node(simple_action_client 
  PLUGIN "ros2_cpp_examples::SimpleActionClient" 
  EXECUTABLE simple_action_client_node
)

install(TARGETS
  simple_publisher
  simple_subscriber
  simple_qos_publisher
  simple_qos_subscriber
  DESTINATION lib/${PROJECT_NAME}
)

 install(TARGETS
 simple_serial_receiver
 simple_serial_transmitter
 ARCHIVE DESTINATION lib
 LIBRARY DESTINATION lib
 RUNTIME DESTINATION bin
 DESTINATION lib/${PROJECT_NAME}
)

install(TARGETS
  simple_action_server
  simple_action_client
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  set(ament_cmake_copyright_FOUND TRUE)
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
