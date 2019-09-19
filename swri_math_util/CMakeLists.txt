cmake_minimum_required(VERSION 3.5)
project(swri_math_util)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)

# The Boost Random library headers and namespaces changed between version
# 1.46 and 1.47
find_package(Boost COMPONENTS random)
if(${Boost_LIB_VERSION} MATCHES "1_46*")
  add_definitions( -DBOOST_1_46 )
endif(${Boost_LIB_VERSION} MATCHES "1_46*")

include_directories(
  include
  ${rclcpp_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)
ament_export_include_directories(include)

#[[

add_library(${PROJECT_NAME}
  src/math_util.cpp
  src/trig_util.cpp
  src/random.cpp
  src/interpolation_1d.cpp)
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES} ${Boost_LIBRARIES})

if(CATKIN_ENABLE_TESTING)
  find_package(rostest REQUIRED)

  add_rostest_gtest(test_trig_util launch/trig_util.test test/test_trig_util.cpp)
  target_link_libraries(test_trig_util ${PROJECT_NAME})

  add_rostest_gtest(test_math_util launch/math_util.test test/test_math_util.cpp)
  target_link_libraries(test_math_util ${PROJECT_NAME})

  add_rostest_gtest(test_random launch/random.test test/test_random.cpp)
  target_link_libraries(test_random ${PROJECT_NAME})
endif()

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

install(TARGETS ${PROJECT_NAME}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

]]

### Install Header Files ###
install(
  DIRECTORY include/
  DESTINATION include
)

ament_package()
