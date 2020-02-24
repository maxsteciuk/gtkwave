find_package(GTK2 REQUIRED)
find_package(X11 REQUIRED)

include_directories(. src src/helpers src/helpers/fst src/libz src/libbz2 src/liblzma src/cocoa)
file(GLOB MAIN_SRC src/*.c)
file(GLOB FST_SRC src/helpers/fst/*.c)
file(GLOB LXT_SRC src/helpers/lxt*.c)
file(GLOB VZT_SRC src/helpers/vzt*.c)
file(GLOB LZMA_SRC src/liblzma/*.c)
list(FILTER MAIN_SRC EXCLUDE REGEX "treesearch_gtk1.c")
list(FILTER MAIN_SRC EXCLUDE REGEX "treesearch_gtk2.c")
#TODO:
list(FILTER MAIN_SRC EXCLUDE REGEX "twinwave.c")

#TODO:
#file(GLOB TWINWAVE_SRC src/*.c)
#list(FILTER TWINWAVE_SRC EXCLUDE REGEX "treesearch_gtk1.c")
#list(FILTER TWINWAVE_SRC EXCLUDE REGEX "treesearch_gtk2.c")
#list(FILTER TWINWAVE_SRC EXCLUDE REGEX "main.c")

set(CONFIGURE_CMD 
    ${CMAKE_SOURCE_DIR}/gtkwave3-gtk3/configure 
                       --enable-tcl=no
)
add_custom_target(configure-gtk3 ALL
                  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/gtkwave3-gtk3
                  COMMAND ${CONFIGURE_CMD}
)

add_library(fst-gtk3 ${FST_SRC})
add_library(lxt-gtk3 ${LXT_SRC})
add_library(vzt-gtk3 ${VZT_SRC})
add_library(lzma-gtk3 ${LZMA_SRC})
add_executable(gtkwave3-gtk3 ${MAIN_SRC})

#TODO:
#add_executable(gtkwave3-gtk3_twin ${TWINWAVE_SRC})

add_dependencies(fst configure-gtk3)
add_dependencies(lxt configure-gtk3 bz2)
add_dependencies(vzt configure-gtk3 bz2)
add_dependencies(lzma configure-gtk3)
add_dependencies(gtkwave3-gtk3 fst-gtk3 lxt-gtk3 vzt-gtk3 lzma-gtk3)

#TODO:
#add_dependencies(gtkwave3-gtk3_twin fst-gtk3 lxt-gtk3 vzt-gtk3 bz2-gtk3 lzma-gtk3)

target_link_libraries(gtkwave3-gtk3 fst-gtk3 z pthread lxt-gtk3 vzt-gtk3 m lzma-gtk3 bz2)

#TODO:
#target_link_libraries(gtkwave3-gtk3_twin fst-gtk3 z pthread lxt-gtk3 vzt-gtk3 bz2-gtk3 m lzma-gtk3 bz2)

if(GTK2_FOUND)
    target_include_directories(gtkwave3-gtk3 PUBLIC ${GTK2_INCLUDE_DIRS})
    target_link_libraries(gtkwave3-gtk3 ${GTK2_LIBRARIES})
    #TODO:
    #target_include_directories(gtkwave3-gtk3_twin PUBLIC ${GTK2_INCLUDE_DIRS})
    #target_link_libraries(gtkwave3-gtk3_twin ${GTK2_LIBRARIES})
endif()

if(GTK3_FOUND)
    target_include_directories(gtkwave3-gtk3 PUBLIC ${GTK3_INCLUDE_DIRS})
    target_link_libraries(gtkwave3-gtk3 ${GTK3_LIBRARIES})
    #TODO:
    #target_include_directories(gtkwave3-gtk3_twin PUBLIC ${GTK3_INCLUDE_DIRS})
    #target_link_libraries(gtkwave3-gtk3_twin ${GTK3_LIBRARIES})
endif()

if(X11_FOUND)
    target_include_directories(gtkwave3-gtk3 PUBLIC ${X11_INCLUDE_DIRS})
    target_link_libraries(gtkwave3-gtk3 ${X11_LIBRARIES})
endif()

