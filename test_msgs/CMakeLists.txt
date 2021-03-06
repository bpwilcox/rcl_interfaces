cmake_minimum_required(VERSION 3.5)

project(test_msgs)

# Default to C11
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 11)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(action_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)

set(msg_files
  "msg/BoundedArrayNested.msg"
  "msg/BoundedArrayPrimitives.msg"
  "msg/Builtins.msg"
  "msg/DynamicArrayNested.msg"
  "msg/DynamicArrayPrimitives.msg"
  "msg/DynamicArrayPrimitivesNested.msg"
  "msg/DynamicArrayStaticArrayPrimitivesNested.msg"
  "msg/Empty.msg"
  "msg/Nested.msg"
  "msg/Primitives.msg"
  "msg/StaticArrayNested.msg"
  "msg/StaticArrayPrimitives.msg"
)

set(srv_files
  "srv/Empty.srv"
  "srv/Primitives.srv"
)

set(action_files
  "action/Fibonacci.action"
)

rosidl_generate_interfaces(${PROJECT_NAME}
  ${msg_files}
  ${srv_files}
  ${action_files}
  DEPENDENCIES builtin_interfaces action_msgs
  ADD_LINTER_TESTS
)

add_library(test_msgs_hardcoded_typesupport SHARED
  src/fibonacci__typesupport.cpp
  src/fibonacci__typesupport.c)
add_dependencies(test_msgs_hardcoded_typesupport
  ${PROJECT_NAME}
)
target_link_libraries(test_msgs_hardcoded_typesupport
  ${_AMENT_EXPORT_LIBRARIES})
target_include_directories(test_msgs_hardcoded_typesupport PUBLIC
  ${CMAKE_CURRENT_BINARY_DIR}/rosidl_typesupport_c
  ${CMAKE_CURRENT_BINARY_DIR}/rosidl_generator_c
  ${CMAKE_CURRENT_BINARY_DIR}/rosidl_typesupport_cpp
  ${CMAKE_CURRENT_BINARY_DIR}/rosidl_generator_cpp
  include
)
# Separate visibility macro because this is a separate library from one created
# by rosidl_generator_c for messages and services
target_compile_definitions(test_msgs_hardcoded_typesupport
  PRIVATE "ROSIDL_GENERATOR_C_BUILDING_DLL_test_msgs_ACTION")

ament_target_dependencies(test_msgs_hardcoded_typesupport
  "rosidl_generator_c"
  "rosidl_typesupport_c"
  "rosidl_generator_cpp"
  "action_msgs"
)

ament_export_libraries(test_msgs_hardcoded_typesupport)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

install(
  TARGETS test_msgs_hardcoded_typesupport
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

if(DEFINED PYTHON_INSTALL_DIR)
  install(DIRECTORY src/test_msgs
    DESTINATION "${PYTHON_INSTALL_DIR}/")
endif()

install(DIRECTORY include/
  DESTINATION include)

ament_export_dependencies(rosidl_default_runtime)
ament_export_include_directories(include)
ament_package()
