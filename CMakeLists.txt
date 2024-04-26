cmake_minimum_required(VERSION 3.5)
project(cmr_tests_utils)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

if(BUILD_TESTING)
  # find dependencies
  find_package(ament_cmake REQUIRED)
  find_package(rclcpp_lifecycle REQUIRED)
  find_package(rclcpp REQUIRED)
  find_package(rclcpp_action REQUIRED)
  find_package(geometry_msgs REQUIRED)
  find_package(tf2_ros REQUIRED)
  find_package(tf2 REQUIRED)
  find_package(tf2_geometry_msgs REQUIRED)
  find_package(nav_msgs REQUIRED)

  set(dependencies
    "rclcpp_lifecycle"
    "rclcpp"
    "rclcpp_action"
    "geometry_msgs"
    "tf2_ros"
    "tf2"
    "tf2_geometry_msgs"
    "nav_msgs"
  )

  include_directories(include)

  set(LIBRARY_NAME "${PROJECT_NAME}_lib")

  add_library(${LIBRARY_NAME} INTERFACE)

  target_include_directories(${LIBRARY_NAME} INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
  )

  ament_target_dependencies(${LIBRARY_NAME} INTERFACE
    ${dependencies}
  )

  ament_export_include_directories(include)
  ament_export_dependencies(${dependencies})
  ament_export_targets("export_${PROJECT_NAME}" HAS_LIBRARY_TARGET)


  install(TARGETS ${LIBRARY_NAME}
    EXPORT "export_${PROJECT_NAME}"
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
    INCLUDES DESTINATION include
  )

  install(DIRECTORY include/${PROJECT_NAME}/
    DESTINATION include/${PROJECT_NAME}
  )


  find_package(ament_cmake_gtest REQUIRED)
  find_package(ament_cmake_pytest REQUIRED)
  add_subdirectory(test)


  ament_package()
endif()
