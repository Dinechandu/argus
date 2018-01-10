cmake_minimum_required (VERSION 2.8)
project (argus)

find_package(OpenCV REQUIRED)
find_package(Threads REQUIRED)

include_directories(
  ${OpenCV_INCLUDE_DIRS}
)

#set(CMAKE_BUILD_TYPE Release)
set(CMAKE_BUILD_TYPE Debug)

##version number
set (argusd_VERSION_MAJOR 0)
set (argusd_VERSION_MINOR 4)

#build options
if(${CMAKE_BUILD_TYPE} STREQUAL "Release")
option(ENABLE_LOG
	"Enable debugging" OFF)
else()
	option(ENABLE_LOG "Enable debugging" ON)
endif()
if(ENABLE_LOG)
	add_definitions(-DENABLE_LOG)
endif()


#miscellaneous
set (BUFFER_SIZE 256)
set (DEFAULT_PORT 12300)

#configure file to add CMake settings to it
configure_file (
	"${PROJECT_SOURCE_DIR}/config.h.in"
	"${PROJECT_SOURCE_DIR}/config.h"
	)


if( WIN32 ) #WINDOWS
    if( ${CMAKE_BUILD_TYPE} STREQUAL "Release")
	    set( CMAKE_CXX_FLAGS "-Wl,-s -Wno-write-strings -Ofast -std=gnu++11 -DWIN32 -mwindows")
    else()
	    set( CMAKE_CXX_FLAGS "-Wno-write-strings -Wall -g -DDEBUG -std=gnu++11 -DWIN32 -mwindows")
    endif()
else()     #LINUX
    if( ${CMAKE_BUILD_TYPE} STREQUAL "Release")
	    set( CMAKE_CXX_FLAGS "-Wl,-s -lsqlite3 -Wno-write-strings -fPIC -Wall -lrt -Ofast -march=native -mtune=native -std=c++11")
    else()
	    set( CMAKE_CXX_FLAGS " -Wno-write-strings -lsqlite3 -lrt -Wall -ggdb3 -DDEBUG -std=c++11")
    endif()
endif()

ADD_SUBDIRECTORY(Node ${CMAKE_CURRENT_SOURCE_DIR}/Node)
ADD_SUBDIRECTORY(clog ${CMAKE_CURRENT_SOURCE_DIR}/clog)

#include_directories ("${PROJECT_BINARY_DIR}")
ADD_EXECUTABLE(argusd argusd.cpp ${FLTK_INCLUDE_DIRS})

TARGET_LINK_LIBRARIES(argusd ${CMAKE_THREAD_LIBS_INIT} -ldl)
TARGET_LINK_LIBRARIES(argusd clog)
TARGET_LINK_LIBRARIES(argusd Node)

TARGET_LINK_LIBRARIES(argusd clog)
TARGET_LINK_LIBRARIES(argusd Node)