cmake_minimum_required(VERSION 3.0.2)
project(urdf2or)

list(INSERT CMAKE_MODULE_PATH 0 "${PROJECT_SOURCE_DIR}/cmake") 

find_package(catkin REQUIRED COMPONENTS
    srdfdom
    urdf
)

find_package(Boost REQUIRED COMPONENTS
    filesystem
    system
)
find_package(OpenRAVE REQUIRED)
find_package(TinyXML2 REQUIRED)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(CATKIN_ENABLE_TESTING false)
catkin_package(
      LIBRARIES ${PROJECT_NAME}
#   INCLUDE_DIRS include
  DEPENDS TinyXML2
)
#catkin_add_env_hooks("20.${PROJECT_NAME}"
#   SHELLS sh
#   DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/catkin-env-hooks"
#)

include_directories(
    ${Boost_INCLUDE_DIRS}
    ${catkin_INCLUDE_DIRS}
    ${TinyXML2_INCLUDE_DIRS}
    ${OpenRAVE_INCLUDE_DIRS}
)
link_directories(
    ${Boost_LIBRARY_DIRS}
    ${catkin_LIBRARY_DIRS}
    ${OpenRAVE_LIBRARY_DIRS}
)

add_library("${PROJECT_NAME}" MODULE
    src/urdf_loader.cpp
    #src/catkin_finder.cpp
)
# set_target_properties(${PROJECT_NAME} PROPERTIES
#     PREFIX ""
#     COMPILE_FLAGS "${OpenRAVE_CXX_FLAGS}"
#     LINK_FLAGS "${OpenRAVE_LINK_FLAGS}")
target_link_libraries("${PROJECT_NAME}"
    ${catkin_LIBRARIES}
    ${TinyXML2_LIBRARIES}
    ${OpenRAVE_LIBRARIES}
)

install(TARGETS ${PROJECT_NAME}
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
install(DIRECTORY src/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

if(CATKIN_ENABLE_TESTING)
    catkin_add_nosetests(tests)
endif(CATKIN_ENABLE_TESTING)

install(TARGETS "${PROJECT_NAME}"
    LIBRARY DESTINATION "share/openrave-${OpenRAVE_LIBRARY_SUFFIX}/plugins"
)

