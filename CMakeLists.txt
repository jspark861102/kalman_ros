cmake_minimum_required(VERSION 2.8.4)
project(kalman)

add_compile_options(-std=c++17 -Ofast)

INCLUDE(cmake/boost.cmake)
INCLUDE(cmake/python.cmake)
INCLUDE(cmake/ide.cmake)
INCLUDE(cmake/apple.cmake)

#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_BUILD_TYPE Release)
LIST( APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}" )


find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  roslib  
)
find_package(Eigen3 REQUIRED)

catkin_package(
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS
    roscpp        
  DEPENDS system_lib
)


###########
## Build ##
###########

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
)


## library
SET(${PROJECT_NAME}_HEADERS  
  kalman.hpp
)
SET(${PROJECT_NAME}_SOURCES  
  kalman.cpp
)

ADD_SOURCE_GROUP(${PROJECT_NAME}_HEADERS)
ADD_SOURCE_GROUP(${PROJECT_NAME}_SOURCES)
ADD_LIBRARY(${PROJECT_NAME} SHARED ${${PROJECT_NAME}_SOURCES} ${${PROJECT_NAME}_HEADERS})
TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME} SYSTEM PUBLIC $<INSTALL_INTERFACE:include> ${EIGEN3_INCLUDE_DIR})
TARGET_LINK_LIBRARIES(${PROJECT_NAME} PUBLIC -lpthread)

### exe 
add_executable(kalman_test kalman-test.cpp)
target_link_libraries(kalman_test ${PROJECT_NAME} ${catkin_LIBRARIES})
TARGET_INCLUDE_DIRECTORIES(kalman_test SYSTEM PUBLIC $<INSTALL_INTERFACE:include> ${EIGEN3_INCLUDE_DIR})

#############
## Install ##
#############
install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
install(DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
install(DIRECTORY rviz
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
