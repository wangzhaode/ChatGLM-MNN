project(chatglm_mnn)

cmake_minimum_required(VERSION 3.10)

# set(MNN_DIR ${CMAKE_SOURCE_DIR}/mnn/${ANDROID_ABI}/lib/cmake/ncnn)
# find_package(MNN REQUIRED)
include_directories(${CMAKE_CURRENT_LIST_DIR}/../../../../../include/)
link_directories(${CMAKE_CURRENT_LIST_DIR}/libs/arm64-v8a)

add_library(chatglm_mnn SHARED chatglm_mnn_jni.cpp ../../../../../src/chat.cpp)

target_link_libraries(chatglm_mnn MNN MNN_Express log)