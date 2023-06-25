cmake_minimum_required(VERSION 2.8.3)
project(lidar_localizer)

find_package(PCL REQUIRED)


find_package(OpenMP)
if(OPENMP_FOUND)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

# find_package(CUDA)
find_package(Eigen3 QUIET)
# find_package(autoware_build_flags REQUIRED)

# AW_CHECK_CUDA()

if(USE_CUDA)
  add_definitions(-DCUDA_FOUND)
  list(APPEND PCL_OPENMP_PACKAGES ndt_gpu)
endif()

if(NOT EIGEN3_FOUND)
  # Fallback to cmake_modules
  find_package(cmake_modules REQUIRED)
  find_package(Eigen REQUIRED)
  set(EIGEN3_INCLUDE_DIRS ${EIGEN_INCLUDE_DIRS})
  set(EIGEN3_LIBRARIES ${EIGEN_LIBRARIES})  # Not strictly necessary as Eigen is head only
  # Possibly map additional variables to the EIGEN3_ prefix.
else()
  set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif()

find_package(catkin REQUIRED COMPONENTS
  ${PCL_OPENMP_PACKAGES}
  # autoware_build_flags
  # autoware_config_msgs
  # autoware_msgs
  jsk_rviz_plugins
  nav_msgs
  ndt
  pcl_conversions
  pcl_ros
  roscpp
  sensor_msgs
  std_msgs
  tf
  velodyne_pointcloud
)

catkin_package(
  CATKIN_DEPENDS
    ${PCL_OPENMP_PACKAGES}
    # autoware_build_flags
    # autoware_config_msgs
    # autoware_msgs
    jsk_rviz_plugins
    ndt
    std_msgs
    velodyne_pointcloud
  DEPENDS PCL
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
)

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")



add_executable(ndt_matching nodes/ndt_matching/ndt_matching.cpp)
target_link_libraries(ndt_matching ${catkin_LIBRARIES})
add_dependencies(ndt_matching ${catkin_EXPORTED_TARGETS})

add_executable(ndt_mapping nodes/ndt_mapping/ndt_mapping.cpp)
target_link_libraries(ndt_mapping ${catkin_LIBRARIES})
add_dependencies(ndt_mapping ${catkin_EXPORTED_TARGETS})

add_executable(voxel_grid_filter nodes/points_downsampler.cpp)

add_dependencies(voxel_grid_filter ${catkin_EXPORTED_TARGETS})

target_link_libraries(voxel_grid_filter ${catkin_LIBRARIES})

add_executable(map_loader nodes/map_loader.cpp)
target_link_libraries(map_loader ${catkin_LIBRARIES} ${PCL_LIBRARIES})

install(
  TARGETS
    ndt_mapping
    ndt_matching
    voxel_grid_filter
    map_loader
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(
  DIRECTORY
    launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
)
install(
  DIRECTORY
    cfgs/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/cfgs
)
install(
  DIRECTORY
    map_data/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/map_data
)