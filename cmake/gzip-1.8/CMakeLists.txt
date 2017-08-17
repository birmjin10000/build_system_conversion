cmake_minimum_required (VERSION 3.9)
project(myGZIP)

set(nodist_libver_a_SOURCES version.c version.h)
set(gzip_SOURCES bits.c deflate.c gzip.c inflate.c lzw.c trees.c unlzh.c unlzw.c unpack.c unzip.c util.c zip.c)
set(SOURCES ${nodist_libver_a_SOURCES} ${gzip_SOURCES})


#add_subdirectory(doc)
add_subdirectory(lib)
#add_subdirectory(tests)

message(${CMAKE_CURRENT_SOURCE_DIR}/lib)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/lib)

add_library(libver version.c)

add_executable(gzip ${gzip_SOURCES})
target_link_libraries(gzip libgzip libver)

