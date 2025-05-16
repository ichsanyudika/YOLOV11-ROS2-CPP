cmake_minimum_required(VERSION 3.5)
project(yolo_ws)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(OpenCV REQUIRED)

# Tentukan path ONNX Runtime manual sesuai lokasi kamu
set(ONNXRUNTIME_INCLUDE_DIR /home/ichan/Documents/lib/onnxruntime-linux-x64-gpu-1.21.0/include)
set(ONNXRUNTIME_LIB /home/ichan/Documents/lib/onnxruntime-linux-x64-gpu-1.21.0/lib/libonnxruntime.so)

if(NOT EXISTS ${ONNXRUNTIME_INCLUDE_DIR} OR NOT EXISTS ${ONNXRUNTIME_LIB})
  message(FATAL_ERROR "ONNX Runtime not found. Pastikan ONNX Runtime sudah terinstall dan path sudah benar.")
endif()

include_directories(
  include
  include/yolo_ws
  ${OpenCV_INCLUDE_DIRS}
  ${ONNXRUNTIME_INCLUDE_DIR}
)

add_executable(main
  src/main.cpp
  src/yolov8Predictor.cpp
  src/utils.cpp
)

target_include_directories(main PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

target_link_libraries(main
  ${OpenCV_LIBS}
  ${ONNXRUNTIME_LIB}
)

ament_target_dependencies(main
  rclcpp
  geometry_msgs
)

install(TARGETS main
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()