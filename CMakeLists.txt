cmake_minimum_required(VERSION 2.8)

project(stitch)

# Set the path where we look for additional CMake modules
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/etc/cmake)

# Find all the required dependencies
find_package(CURL REQUIRED)
find_package(JPEG)
find_package(PNG)
find_package(GEOTIFF)

# Turn on all compiler warnings
set(CMAKE_C_FLAGS "-Wall")

# Create config.h
configure_file(
	${CMAKE_CURRENT_SOURCE_DIR}/src/config.h.in
	${CMAKE_CURRENT_BINARY_DIR}/src/config.h
)

# Declare final target
add_executable(stitch src/stitch.c)
target_include_directories(stitch
	PRIVATE
	  ${CMAKE_CURRENT_BINARY_DIR}/src
		${CURL_INCLUDE_DIRS} ${PNG_INCLUDE_DIRS} ${JPEG_INCLUDE_DIRS}
		${GEOTIFF_INCLUDE_DIRS}
)
target_link_libraries(stitch
	${CURL_LIBRARIES} ${JPEG_LIBRARIES} ${PNG_LIBRARIES}
	${GEOTIFF_LIBRARIES}
)
