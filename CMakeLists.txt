# Dcraw/CMakeLists.txt
# Created by Robin Rowe 2020-12-15
# License: GNU GENERAL PUBLIC LICENSE

cmake_minimum_required(VERSION 3.8)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

project(Dcraw)
message("Configuring ${PROJECT_NAME}...")
enable_testing()
file(STRINGS sources.cmake SOURCES)
add_library(${PROJECT_NAME}_lib ${SOURCES})
link_libraries(${PROJECT_NAME}_lib)
if(NOT WIN32 AND NOT APPLE)
	link_libraries(rt pthread)
endif(NOT WIN32 AND NOT APPLE)

option(UNISTD "Enable libunistd" true)
if(UNISTD)
# /code/github/libunistd/build/Win32/Debug/libunistd.lib
	set(LIBUNISTD_PATH /code/github/libunistd)
	if(WIN32)
		set(LIB_UNISTD_INC ${LIBUNISTD_PATH}/unistd)
		include_directories(${LIB_UNISTD_INC})
		message("include = ${LIB_UNISTD_INC}")
		link_directories(${LIBUNISTD_PATH}/build/Win32/Debug)
		link_libraries(libunistd)
	endif(WIN32)
endif(UNISTD)

option(FLTK "Enable FLTK" false)
if(FLTK)
	set(FLTK_PATH /Code/fltk)
	include_directories(${FLTK_PATH} ${FLTK_PATH}/fltk-build ${FLTK_PATH}/fltk-src/)
	link_directories(${FLTK_PATH}/fltk-src/build/lib/Debug)
	set(FLTK_LIBS
		fltk_formsd
		fltk_gld
		fltk_imagesd
		fltk_jpegd
		fltk_pngd
		fltk_zd
		fltkd
	)
	link_libraries(${FLTK_LIBS} comctl32)
endif(FLTK)

add_definitions(-D_CRT_SECURE_NO_WARNINGS)

add_executable(dcraw dcraw.c)
add_executable(clean_crw clean_crw.c)
add_executable(decompress decompress.c)
add_executable(elphel_dng elphel_dng.c)
add_executable(scan scan.c)
add_executable(fujiturn fujiturn.c)

option(GIMP "Build GIMP plugin" false)
if(GIMP)
	add_executable(rawphoto rawphoto.c)
endif(GIMP)
option(CINEPAINT "Build CINEPAINT plugin" false)
if(CINEPAINT)
	add_executable(plugin-rawphoto cine-rawphoto.c)
endif(CINEPAINT)

set(TARGET dcraw)
set(LIB libjpeg-turbo)
set(LIBPATH /code/gitlab/cmakelib/build/Win32/${LIB})
include_directories(${LIBPATH}/${LIB}-src ${LIBPATH}/${LIB}-build)
target_link_directories(${TARGET} PRIVATE ${LIBPATH}/${LIB}-build/Debug)
target_link_libraries(${TARGET} jpeg-static)

set(CMAKELIB_BINARY_DIR /code/gitlab/cmakelib/build/Win32)
include(/code/gitlab/cmakelib/cmake/modules/tiff-config.cmake)

set(LIB ZLIB)
set(LIBPATH /code/gitlab/cmakelib/build/Win32/${LIB})
include_directories(${LIBPATH}/${LIB}-src ${LIBPATH}/${LIB}-build)
target_link_directories(${TARGET} PRIVATE ${LIBPATH}/${LIB}-build/Debug)
target_link_libraries(${TARGET} zlibstaticd)



