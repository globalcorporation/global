cmake_minimum_required(VERSION 3.16)

project(global_library VERSION 0.0.1 LANGUAGES C CXX)
set(CMAKE_CXX_STANDARD 20)
 
message(STATUS "START COMPILE PACKAGE_TEMPLATE")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")

set(global_shared_dir "lib")
set(global_exe_dir "bin")
set(prefix_shared_library "lib")
 

if (ANDROID)
 set(package_shared_extension "_${ANDROID_ARCH_NAME}.so")
 set(package_exe_extension "")
 set(package_name "global_${ANDROID_ARCH_NAME}")
elseif (WIN32)
 set(package_shared_extension ".dll")
 set(package_exe_extension ".exe") 
 set(package_name "global")
elseif (APPLE)
 set(package_shared_extension ".dylib")
 set(package_exe_extension "") 
 set(package_name "global")
else()
 set(package_shared_extension ".so")
 set(package_exe_extension "") 
 set(package_name "global")
endif()


add_library(global_shared SHARED global.cpp)
add_executable(global_exe global.cpp)

set_target_properties(global_shared PROPERTIES 
  LIBRARY_OUTPUT_DIRECTORY "${global_shared_dir}"
  PREFIX "${prefix_shared_library}"
  OUTPUT_NAME "${package_name}"
)

set_target_properties(global_exe PROPERTIES 
  RUNTIME_OUTPUT_DIRECTORY "${global_exe_dir}"
  OUTPUT_NAME "${package_name}"
)

if (WIN32)
 set_target_properties(global_shared PROPERTIES
  WINDOWS_EXPORT_ALL_SYMBOLS ON
)
endif (WIN32)
 
add_custom_command(
  TARGET global_shared POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${global_shared_dir}/${prefix_shared_library}global${package_shared_extension}"
)

add_custom_command(
  TARGET global_exe POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${global_exe_dir}/global${package_exe_extension}" 
)

 