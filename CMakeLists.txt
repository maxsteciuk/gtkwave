cmake_minimum_required(VERSION 3.15)
project(gtkwave)

include(ExternalProject)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -ggdb -O3 -Wall")

execute_process(COMMAND git submodule add https://github.com/maxsteciuk/gnome-cmake
)
execute_process(COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_BINARY_DIR}/gnome-cmake/modules ${CMAKE_SOURCE_DIR}/gnome-modules
)

set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_SOURCE_DIR}/gnome-modules")

find_package(Intl REQUIRED)
find_package(GTK3)

add_definitions(-DFST_WRITER_PARALLEL -D_THREAD_SAFE)

add_subdirectory(gtkwave3)

if(GTK3_FOUND)
    add_subdirectory(gtkwave3-gtk3)
endif()

if(Intl_FOUND)
    target_include_directories(gtkwave3 PUBLIC ${Intl_INCLUDE_DIRS})
    target_link_libraries(gtkwave3 ${Intl_LIBRARIES})
    target_include_directories(gtkwave3-gtk3 PUBLIC ${Intl_INCLUDE_DIRS})
    target_link_libraries(gtkwave3-gtk3 ${Intl_LIBRARIES})
endif()
