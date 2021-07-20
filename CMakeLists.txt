cmake_minimum_required(VERSION 3.5)

project(message_logger)

find_package(ament_cmake_auto REQUIRED)
ament_auto_find_build_dependencies()

include(CMakeDependentOption)
cmake_dependent_option(MELO_USE_COUT "Use std::cout" OFF
                       "DEFINED ENV{ROS_DISTRO}" ON)

if(NOT MELO_USE_COUT)
  message(WARNING "Building message_logger using ros.")
else()
  message(WARNING "Building message_logger using std::cout.")
endif()

configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/include/message_logger/log/log_messages_backend_config.hpp.in
  ${CMAKE_CURRENT_BINARY_DIR}/include/message_logger/log/log_messages_backend_config.hpp @ONLY
)

if(DEFINED MELO_FUNCTION_PRINTS)
  add_definitions(-DMELO_FUNCTION_PRINTS)
endif()

# Build
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

ament_auto_add_library(${PROJECT_NAME}
  src/log/globals.cpp
  src/time/Time.cpp
  src/time/TimeStd.cpp
)

# Install
install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/include/message_logger/log/log_messages_backend_config.hpp
  DESTINATION include/message_logger/log
)

ament_auto_package()

# Test
if(BUILD_TESTING)
  find_package(ament_cmake_gtest REQUIRED)
  ament_add_gtest(empty_test test/EmptyTests.cpp)
  target_link_libraries(empty_test)
endif()
