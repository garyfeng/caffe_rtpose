#Set minimum version requered
cmake_minimum_required(VERSION 2.4.6)
#just to avoid the warning
if(COMMAND cmake_policy)
     cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)
#set project name
project(conv-pose-machines)
#Append path to the module path
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR})
#OpenCV & Caffe
find_package(OpenCV REQUIRED)
set(Caffe_DIR /opt/caffe/build)
find_package(Caffe REQUIRED)
find_package(CUDA REQUIRED)
#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/../bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/../libs)
#set the include directories
include_directories (${OpenCV_INCLUDE_DIRS} ${Caffe_INCLUDE_DIRS})

# CUDA C++11 library
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-gencode arch=compute_20,code=sm_20;--std=c++11)
cuda_add_library(renderFu ${PROJECT_SOURCE_DIR}/../src/rtpose/renderFunctions.cu)

# Use C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=gnu++0x")
# Libraries
add_library(model ${PROJECT_SOURCE_DIR}/../src/rtpose/modelDescriptor.cpp)
add_library(modelFactory ${PROJECT_SOURCE_DIR}/../src/rtpose/modelDescriptorFactory.cpp)
# Executable
add_executable(rtpose rtpose/rtpose.cpp)
# Link
target_link_libraries(rtpose model modelFactory renderFu ${Caffe_LIBRARIES} ${OpenCV_LIBS} -lpthread)

