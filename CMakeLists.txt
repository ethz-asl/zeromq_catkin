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
  DEPENDS zeromq
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND ""
  BUILD_COMMAND ""
  INSTALL_COMMAND mkdir -p ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp && cp ../zeromq_cpp/zmq.hpp ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp/zmq.hpp
)

# TODO(dymczykm, tcies) would be cool to get rid of this everywhere
link_directories(${CATKIN_DEVEL_PREFIX}/lib)

cs_add_library(${PROJECT_NAME}_dependency_tracker src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME}_dependency_tracker zeromq zeromq_cpp)
target_link_libraries(${PROJECT_NAME}_dependency_tracker zmq)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include)
