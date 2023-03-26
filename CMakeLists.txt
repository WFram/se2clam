cmake_minimum_required(VERSION 2.8.3)
project(se2clam)

add_subdirectory(Thirdparty/DBoW2)
add_subdirectory(Thirdparty/g2o)

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/CMakeModules/)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x -Wall")

find_package(Eigen REQUIRED)
find_package(OpenCV REQUIRED)
find_package(CSparse REQUIRED)
find_package(Cholmod REQUIRED)

find_package(catkin REQUIRED COMPONENTS
        geometry_msgs
        nav_msgs
        roscpp
        rospy
        tf
        roslib
        cv_bridge
        cmake_modules
        image_transport
        message_generation
        )

catkin_package(

)

include_directories(
        ${catkin_INCLUDE_DIRS}
)
list(APPEND LINK_LIBS
        ${catkin_LIBRARIES}
        )

set(G20_INCLUDE_DIRS
        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o
        )

include_directories(
        include/se2clam/
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${EIGEN_INCLUDE_DIRS}
        ${OpenCV_INCLUDE_DIRS}
        ${CSPARSE_INCLUDE_DIR}
        ${Cholmod_INCLUDE_DIR}
        ${G20_INCLUDE_DIRS}
)

list(APPEND LINK_LIBS
        ${OpenCV_LIBS}
        )

list(APPEND G2O_LIBS
        cxsparse
        cholmod
        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib
        )

#list(APPEND G2O_LIBS
#        cxsparse
#        cholmod
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_solver_slam2d_linear.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_types_icp.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_types_slam2d.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_core.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_solver_csparse.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_solver_structure_only.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_types_sba.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_types_slam3d.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_csparse_extension.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_solver_dense.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_stuff.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_types_sclam2d.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_solver_pcg.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_types_data.so
#        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o_types_sim3.so
#        )

message("G20_INCLUDE_DIRS: ${G20_INCLUDE_DIRS}")
message("G2O_LIBS: ${G2O_LIBS}")

FILE(GLOB_RECURSE IncFiles "include/se2clam/*.h")
aux_source_directory(src/. DIR_SRCS)
add_library(se2clam ${DIR_SRCS} ${IncFiles})

target_link_libraries(se2clam
        ${LINK_LIBS}
        ${G2O_LIBS}
        DBoW2se2clam
        )

add_executable(test_vn test/test_vn.cpp)

target_link_libraries(test_vn
        se2clam
        ${LINK_LIBS}
        ${G2O_LIBS}
        )

add_executable(datapub test/datapub.cpp)
target_link_libraries(datapub
        se2clam
        ${LINK_LIBS}
        )

add_executable(imgview test/imgview.cpp)
target_link_libraries(imgview
        se2clam
        ${LINK_LIBS}
        )

add_executable(test_ros test/test_ros.cpp)
target_link_libraries(test_ros
        se2clam
        ${LINK_LIBS}
        )
