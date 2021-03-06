cmake_minimum_required(VERSION 3.1)

project(SimDS)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE Release)
endif ()

message(STATUS "BUILD_TYPE          is ${CMAKE_BUILD_TYPE}")
message(STATUS "CMAKE_CXX_STANDARD  is ${CMAKE_CXX_STANDARD}")
message(STATUS "CXX_FLAGS           are ${CMAKE_CXX_FLAGS}")
message(STATUS "CXX_FLAGS_DEBUG     are ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS "CXX_FLAGS_RELEASE   are ${CMAKE_CXX_FLAGS_RELEASE}")

set(SDS_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)

file(GLOB HEADERS ${SDS_INCUDE_DIR}/sim_ds.hpp ${SDS_INCLUDE_DIR}/sim_ds/*.hpp)

set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)
find_package(Boost REQUIRED thread)
if(Boost_FOUND)
    message(STATUS "!!! BOOST FOUND !!!")
else()
    message(STATUS "!!! BOOST NOT FOUND !!!")
endif()

# Build Public Interface Library
add_library(sim_ds INTERFACE)
target_sources(sim_ds INTERFACE ${HEADERS})
#set_target_properties(sim_ds PROPERTIES LINKER_LANGUAGE CXX)
#include_directories(${SDS_INCLUDE_DIR})
target_include_directories(sim_ds INTERFACE ${SDS_INCLUDE_DIR} ${Boost_INCLUDE_DIR})
target_link_libraries(sim_ds INTERFACE ${Boost_LIBRARIES})


# Build Private Static Library for Development on Xcode
add_library(sim_ds_dev STATIC ${SDS_INCLUDE_DIR}/sim_ds.cpp ${HEADERS})
include_directories(${SDS_INCLUDE_DIR} ${Boost_INCLUDE_DIR})
target_link_libraries(sim_ds_dev ${Boost_LIBRARIES})


if(${CMAKE_CURRENT_SOURCE_DIR} STREQUAL ${CMAKE_SOURCE_DIR})
	message(STATUS "Enable test SimDS")
	enable_testing()
	add_subdirectory(test)
endif()

