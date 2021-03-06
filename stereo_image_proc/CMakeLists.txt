cmake_minimum_required(VERSION 2.8)
project(stereo_image_proc)

find_package(catkin REQUIRED)
catkin_package(LIBRARIES ${PROJECT_NAME})

include_directories(include)

# Dynamic reconfigure support
find_package(catkin REQUIRED dynamic_reconfigure)
generate_dynamic_reconfigure_options(cfg/Disparity.cfg)

find_package(catkin REQUIRED dynamic_reconfigure image_geometry image_proc image_transport message_filters nodelet sensor_msgs stereo_msgs)
find_package(OpenCV REQUIRED)
include_directories(SYSTEM ${catkin_INCLUDE_DIRS}
                           ${OpenCV_INCLUDE_DIRS}
)

# Nodelet library
add_library(${PROJECT_NAME} src/libstereo_image_proc/processor.cpp src/nodelets/disparity.cpp src/nodelets/point_cloud2.cpp src/nodelets/point_cloud.cpp)
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES}
                                      ${OpenCV_LIBRARIES}
)
add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}_gencfg)
install(TARGETS ${PROJECT_NAME}
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
install(FILES nodelet_plugins.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

# Standalone node
add_executable(stereoimageproc_exe src/nodes/stereo_image_proc.cpp)
target_link_libraries(stereoimageproc_exe stereo_image_proc)
SET_TARGET_PROPERTIES(stereoimageproc_exe PROPERTIES OUTPUT_NAME stereo_image_proc)
install(TARGETS stereoimageproc_exe
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

# install the launch file
install(DIRECTORY launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/
)
