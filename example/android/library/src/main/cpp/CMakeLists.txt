cmake_minimum_required(VERSION 3.4.1)

set(TOP_DIR "${CMAKE_SOURCE_DIR}/../../../../../..")
set(LIB_DIR "${TOP_DIR}/lib/android")

# cflags: compile paramters
set(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS}   -std=gnu99 -Wall -Werror")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Werror")

# mbedtls
add_library(mbedtls SHARED IMPORTED)
set_target_properties(mbedtls PROPERTIES IMPORTED_LOCATION "${LIB_DIR}/${ANDROID_ABI}/libmbedtls.so")

# sysutils
add_library(sysutils SHARED IMPORTED)
set_target_properties(sysutils PROPERTIES IMPORTED_LOCATION "${LIB_DIR}/${ANDROID_ABI}/libsysutils.so")

# liteplayer_adapter
set(LITEPLAYER_ADAPTER_SRC
    ${TOP_DIR}/adapter/source_httpclient_wrapper.c
    ${TOP_DIR}/adapter/source_file_wrapper.c
    ${TOP_DIR}/adapter/sink_opensles_wrapper.cpp)
add_library(liteplayer_adapter STATIC ${LITEPLAYER_ADAPTER_SRC})
target_include_directories(liteplayer_adapter PRIVATE
    ${TOP_DIR}/thirdparty/sysutils/include ${TOP_DIR}/include)
target_link_libraries(liteplayer_adapter sysutils OpenSLES)

# liteplayer_core
add_library(liteplayer_core SHARED IMPORTED)
set_target_properties(liteplayer_core PROPERTIES IMPORTED_LOCATION "${LIB_DIR}/${ANDROID_ABI}/libliteplayer_core.so")

# liteplayer-jni
add_library(liteplayer-jni SHARED liteplayer-jni.cpp)
target_include_directories(liteplayer-jni PRIVATE ${TOP_DIR}/include ${TOP_DIR}/adapter)
target_link_libraries(liteplayer-jni liteplayer_core liteplayer_adapter sysutils mbedtls android log)
