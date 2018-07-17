cmake_minimum_required(VERSION 2.8.3)
project(zeromq_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(zeromq_src
  URL https://github.com/zeromq/libzmq/releases/download/v4.2.2/zeromq-4.2.2.tar.gz
  CONFIGURE_COMMAND cd ../zeromq_src && ./configure --with-pic --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../zeromq_src && make -j8
  INSTALL_COMMAND cd ../zeromq_src && make install -j8
)

ExternalProject_Add(zeromq_cpp
  GIT_REPOSITORY https://github.com/zeromq/cppzmq.git
  GIT_TAG d9f0f016c07046742738c65e1eb84722ae32d7d4
  DEPENDS zeromq_src
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND ""
  BUILD_COMMAND ""
  INSTALL_COMMAND mkdir -p ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp && cp ../zeromq_cpp/zmq.hpp ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp/zmq.hpp
)

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/zeromq_cpp
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})
install(FILES ${CATKIN_DEVEL_PREFIX}/include/zmq.h
              ${CATKIN_DEVEL_PREFIX}/include/zmq_utils.h
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libzmq*")
cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          LIBRARIES zmq)
