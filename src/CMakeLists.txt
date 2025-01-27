# The Flutter tooling requires that developers have CMake 3.10 or later
# installed. You should not increase this version, as doing so will cause
# the plugin to fail to compile for some customers of the plugin.
cmake_minimum_required(VERSION 3.10)

project(whisper_dart_library VERSION 0.0.1 LANGUAGES C CXX)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")

if(CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=armv8.2-a+fp16")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=armv8.2-a+fp16")
endif()

add_library(whisper_dart SHARED main.cpp)
add_library(whisper whisper.cpp/whisper.cpp)
add_library(ggml whisper.cpp/ggml.c)

set_target_properties(whisper_dart PROPERTIES
  PUBLIC_HEADER whisper.cpp/whisper.h 
  OUTPUT_NAME "whisper_dart"
)

if (WIN32)
set_target_properties(whisper_dart PROPERTIES
  WINDOWS_EXPORT_ALL_SYMBOLS ON
)
endif (WIN32)

target_compile_definitions(whisper_dart PUBLIC DART_SHARED_LIB)
target_link_libraries(whisper_dart PRIVATE whisper ggml ${CMAKE_THREAD_LIBS_INIT})
