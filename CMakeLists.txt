cmake_minimum_required(VERSION 2.8.3)
project(zeromq_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(zeromq_src
  URL http://archive.org/download/zeromq_4.0.7/zeromq-4.0.7.tar.gz
  URL_MD5 9b46f7e7b0704b83638ef0d461fd59ab
  CONFIGURE_COMMAND cd ../zeromq_src && ./configure --with-pic --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../zeromq_src && make -j8
  INSTALL_COMMAND cd ../zeromq_src && make install -j8
)

ExternalProject_Add(zeromq_cpp
  GIT_REPOSITORY https://github.com/zeromq/cppzmq.git
  GIT_TAG 4d79066be3c3ed0cbf247d3a2eb04be2b803eb3c
  DEPENDS zeromq_src
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND ""
  BUILD_COMMAND ""
  INSTALL_COMMAND mkdir -p ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp && cp ../zeromq_cpp/zmq.hpp ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp/zmq.hpp
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} zeromq_src zeromq_cpp)
target_link_libraries(${PROJECT_NAME} 
  ${CATKIN_DEVEL_PREFIX}/lib/libzmq${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          CFG_EXTRAS zeromq-extras.cmake)
