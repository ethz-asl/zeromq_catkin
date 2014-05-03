cmake_minimum_required(VERSION 2.8.3)
project(zeromq_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

ExternalProject_Add(zeromq
  URL http://download.zeromq.org/zeromq-4.0.3.tar.gz
  URL_MD5 8348341a0ea577ff311630da0d624d45
  CONFIGURE_COMMAND cd ../zeromq && ./configure --with-pic --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../zeromq && make -j8
  INSTALL_COMMAND cd ../zeromq && make install -j8
)

ExternalProject_Add(zeromq_cpp
  GIT_REPOSITORY https://github.com/zeromq/cppzmq.git
  DEPENDS ZMQ
  CONFIGURE_COMMAND ""
  BUILD_COMMAND ""
  INSTALL_COMMAND cp ../zeromq_cpp/zmq.hpp ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp/zmq.hpp
)

cs_add_library(${PROJECT_NAME}_dependency_tracker src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME}_dependency_tracker zeromq zeromq_cpp)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include)
