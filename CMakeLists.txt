cmake_minimum_required(VERSION 2.8.3)
project(g2o_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/g2o)

ExternalProject_Add(g2o_src
	GIT_REPOSITORY https://github.com/RainerKuemmerle/g2o.git
  # GIT_TAG fix/suitesparse_include
  # GIT_TAG d86dd9d6c31e8f11e113eae90297a4b8958ff393	# add Edge return false
  # GIT_TAG e32ccb05bd8f0187d87ef74ac95d08f56c105c4e	#
  # GIT_TAG 653c9eedc51f73394451cd8f55463c962b4092ab	# fix alignment error and warning
  GIT_TAG 12384734eae994583ab725bd570bcb94e1aa52e6	# fix alignment error and warning
  PREFIX ${CMAKE_CURRENT_BINARY_DIR}/g2o
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX} -DCMAKE_BUILD_TYPE=Release
  -DBUILD_SHARED_LIBS=ON -DG2O_BUILD_APPS=OFF -DG2O_BUILD_EXAMPLES=OFF -DG2O_BUILD_PLAYGROUND=OFF
)

ExternalProject_Get_Property(g2o_src install_dir)
include_directories(${install_dir}/include)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} g2o_src)
target_link_libraries(${PROJECT_NAME})
cs_install()

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/g2o
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.hpp"
  PATTERN "*.h"
  PATTERN ".git" EXCLUDE
)

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/
  CFG_EXTRAS g2o-extras.cmake)
