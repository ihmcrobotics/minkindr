cmake_minimum_required(VERSION 3.16.3)
project(minkindr)

find_package(ament_cmake REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(kindr REQUIRED)

# This is a header-only library
add_library(${PROJECT_NAME} INTERFACE)

target_include_directories(${PROJECT_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

target_link_libraries(${PROJECT_NAME} INTERFACE
  kindr::kindr
  Eigen3::Eigen
)

# Install header files - keep original structure
install(DIRECTORY include/
  DESTINATION include
  FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp"
)

# Install the target
install(TARGETS ${PROJECT_NAME}
  EXPORT export_${PROJECT_NAME}
  INCLUDES DESTINATION include
)

# Tests - disabled by default
option(BUILD_MINKINDR_TESTS "Build minkindr tests" OFF)
if(BUILD_TESTING AND BUILD_MINKINDR_TESTS)
  find_package(ament_cmake_gtest REQUIRED)
  
  ament_add_gtest(${PROJECT_NAME}_tests
    test/test-main.cc
    test/test-rotation.cc
    test/test-sim3.cc
    test/test-transformation.cc
    test/test-transformation-2d.cc
  )
  
  target_link_libraries(${PROJECT_NAME}_tests
    ${PROJECT_NAME}
  )
endif()

ament_export_targets(export_${PROJECT_NAME})
ament_export_dependencies(kindr Eigen3)

ament_package()