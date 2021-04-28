cmake_minimum_required(VERSION 3.16)

project(stitch)

# Set the path where we look for additional CMake modules
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/etc/cmake)

# Optionally let CMake know that we prefer static libraries
set(PREFER_STATIC_LIBRARIES FALSE
	CACHE BOOL "Prefer static libraries over shared ones when linking")
if(PREFER_STATIC_LIBRARIES)
	if(WIN32)
		set(CMAKE_FIND_LIBRARY_SUFFIXES .lib .a ${CMAKE_FIND_LIBRARY_SUFFIXES})
	else(WIN32)
		set(CMAKE_FIND_LIBRARY_SUFFIXES .a ${CMAKE_FIND_LIBRARY_SUFFIXES})
	endif(WIN32)
	if(NOT APPLE)
		set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -static")
	endif(NOT APPLE)
endif(PREFER_STATIC_LIBRARIES)

# Find all the required dependencies
find_package(CURL REQUIRED)
find_package(JPEG)
find_package(PNG)
find_package(TIFF)
find_package(GEOTIFF)

# Turn on all compiler warnings
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall")

# Create config.h
configure_file(
	${CMAKE_CURRENT_SOURCE_DIR}/src/config.h.in
	${CMAKE_CURRENT_BINARY_DIR}/src/config.h
)

# Declare final target
add_executable(stitch src/stitch.c)
target_include_directories(stitch PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/src ${CURL_INCLUDE_DIRS})
target_link_libraries(stitch m ${CURL_LIBRARIES})
if(JPEG_FOUND)
  target_include_directories(stitch PRIVATE ${JPEG_INCLUDE_DIRS})
  target_link_libraries(stitch ${JPEG_LIBRARIES})
endif(JPEG_FOUND)
if(PNG_FOUND)
  target_include_directories(stitch PRIVATE ${PNG_INCLUDE_DIRS})
  target_link_libraries(stitch ${PNG_LIBRARIES})
endif(PNG_FOUND)
if(GEOTIFF_FOUND)
  target_include_directories(stitch PRIVATE ${GEOTIFF_INCLUDE_DIRS} ${TIFF_INCLUDE_DIRS})
  target_link_libraries(stitch ${GEOTIFF_LIBRARIES} ${TIFF_LIBRARIES})
endif(GEOTIFF_FOUND)
