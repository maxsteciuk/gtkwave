find_package(GTK2 REQUIRED)

include_directories(. src src/helpers src/helpers/fst src/libz src/libbz2 src/liblzma src/cocoa)
file(GLOB MAIN_SRC src/*.c)
file(GLOB FST_SRC src/helpers/fst/*.c)
file(GLOB LXT_SRC src/helpers/lxt*.c)
file(GLOB VZT_SRC src/helpers/vzt*.c)
file(GLOB BZ2_SRC src/libbz2/*.c)
file(GLOB LZMA_SRC src/liblzma/*.c)
list(FILTER MAIN_SRC EXCLUDE REGEX "treesearch_gtk1.c")
list(FILTER MAIN_SRC EXCLUDE REGEX "treesearch_gtk2.c")
list(FILTER MAIN_SRC EXCLUDE REGEX "twinwave.c")

#TODO:
#file(GLOB TWINWAVE_SRC src/*.c)
#list(FILTER TWINWAVE_SRC EXCLUDE REGEX "treesearch_gtk1.c")
#list(FILTER TWINWAVE_SRC EXCLUDE REGEX "treesearch_gtk2.c")
#list(FILTER TWINWAVE_SRC EXCLUDE REGEX "main.c")

add_definitions(-DWAVE_USE_GTK2)

set(CONFIGURE_CMD 
    ${CMAKE_SOURCE_DIR}/gtkwave3/configure 
                       --enable-tcl=no
)
add_custom_target(configure ALL
                  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/gtkwave3
                  COMMAND ${CONFIGURE_CMD}
)

add_library(fst ${FST_SRC})
add_library(lxt ${LXT_SRC})
add_library(vzt ${VZT_SRC})
add_library(bz2 ${BZ2_SRC})
add_library(lzma ${LZMA_SRC})
add_executable(gtkwave3 ${MAIN_SRC})

#TODO:
#add_executable(gtkwave3_twin ${TWINWAVE_SRC})

add_dependencies(fst configure)
add_dependencies(lxt configure)
add_dependencies(vzt configure)
add_dependencies(bz2 configure)
add_dependencies(lzma configure)
add_dependencies(gtkwave3 fst lxt vzt bz2 lzma)

#TODO:
#add_dependencies(gtkwave3_twin fst lxt vzt bz2 lzma)

target_link_libraries(gtkwave3 fst z pthread lxt vzt bz2 m lzma)

#TODO:
#target_link_libraries(gtkwave3_twin fst z pthread lxt vzt bz2 m lzma)

if(GTK2_FOUND)
    target_include_directories(gtkwave3 PUBLIC ${GTK2_INCLUDE_DIRS})
    target_link_libraries(gtkwave3 ${GTK2_LIBRARIES})
    #TODO:
    #target_include_directories(gtkwave3_twin PUBLIC ${GTK2_INCLUDE_DIRS})
    #target_link_libraries(gtkwave3_twin ${GTK2_LIBRARIES})
endif()
