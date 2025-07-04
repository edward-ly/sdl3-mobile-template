cmake_minimum_required(VERSION 3.12.0)
project(SDL_helloworld
	VERSION 0.1.0
	LANGUAGES C
)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED True)

# by configuring CMake with -DDOWNLOAD_DEPENDENCIES=ON/OFF,
# users can choose between downloading dependencies or using system libraries
option(DOWNLOAD_DEPENDENCIES "Download dependencies" TRUE)

if(DOWNLOAD_DEPENDENCIES)
	# FetchContent downloads and configures dependencies
	include(FetchContent)
	FetchContent_Declare(
		SDL3
		GIT_REPOSITORY "https://github.com/libsdl-org/SDL.git"
		GIT_TAG "release-3.2.16"
		EXCLUDE_FROM_ALL
	)
	FetchContent_MakeAvailable(SDL3)
else()
	# find_package looks for already-installed system packages,
	# configure with `-DCMAKE_PREFIX_PATH="/path/to/package1;/path/to/package2"`
	# to add search paths
	find_package(SDL3 CONFIG REQUIRED)
endif()

if(ANDROID)
	# SDL applications need to be built as a shared library
	function(add_executable TARGET)
		add_library(${TARGET} SHARED ${ARGN})
	endfunction()
endif()

add_executable(sdl-helloworld main.c)

target_link_libraries(sdl-helloworld PRIVATE SDL3::SDL3)

# this is safe to set on all platforms, otherwise your SDL app will
# have a terminal window pop up with it on Windows
set_property(TARGET sdl-helloworld PROPERTY WIN32_EXECUTABLE TRUE)

if(EMSCRIPTEN)
	# create an HTML webpage
	set_property(TARGET sdl-helloworld PROPERTY SUFFIX ".html")
endif()
