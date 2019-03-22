cmake_minimum_required(VERSION 2.6)
SET(CMAKE_C_COMPILER "clang")
SET(CMAKE_CXX_COMPILER "clang++")

project(cork)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(GMP_PREFIX "" CACHE PATH "path ")


find_path(MPIR_INCLUDE_DIR mpir.h mpirxx.h 
    PATHS ${MPIR_PREFIX}/include /usr/include /usr/local/include )

find_library(MPIR_LIBRARY NAMES mpir libmpir
    PATHS ${MPIR_PREFIX}/lib /usr/lib /usr/local/lib)


if(MPIR_INCLUDE_DIR AND MPIR_LIBRARY)
    get_filename_component(MPIR_LIBRARY_DIR ${MPIR_LIBRARY} PATH)
    set(MPIR_FOUND TRUE)
endif()

if(MPIR_FOUND)
   if(NOT MPIR_FIND_QUIETLY)
      MESSAGE(STATUS "Found MPIR: ${MPIR_LIBRARY}")
   endif()
elseif(MPIR_FOUND)
   if(MPIR_FIND_REQUIRED)
      message(FATAL_ERROR "Could not find GMP")
   endif()
endif()

include_directories( 
  "${CMAKE_SOURCE_DIR}/src" 
  "${CMAKE_SOURCE_DIR}/src/accel" 
  "${CMAKE_SOURCE_DIR}/src/file_formats" 
  "${CMAKE_SOURCE_DIR}/src/isct" 
  "${CMAKE_SOURCE_DIR}/src/math" 
  "${CMAKE_SOURCE_DIR}/src/mesh" 
  "${CMAKE_SOURCE_DIR}/src/rawmesh" 
  "${CMAKE_SOURCE_DIR}/src/util" 
)
link_directories(
  ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}
)

file(GLOB_RECURSE SOURCES
  "${CMAKE_SOURCE_DIR}/src/*.cpp"
)
list(REMOVE_ITEM SOURCES
  "${CMAKE_SOURCE_DIR}/src/main.cpp"
)


add_library (${PROJECT_NAME} STATIC ${SOURCES})
target_link_libraries(${PROJECT_NAME}  ${MPIR_LIBRARY} )
add_executable(${PROJECT_NAME}test "${CMAKE_SOURCE_DIR}/src/main.cpp")
target_link_libraries(${PROJECT_NAME}test  -l${PROJECT_NAME} )