cmake_minimum_required (VERSION 3.9)
project(MyLessInCmake)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

set (SOURCES
  main.c screen.c brac.c ch.c charset.c cmdbuf.c command.c cvt.c decode.c edit.c filename.c forwback.c \\
  help.c ifile.c input.c jump.c line.c linenum.c lsystem.c mark.c optfunc.c option.c opttbl.c os.c \\
  output.c pattern.c position.c prompt.c search.c signal.c tags.c ttyin.c version.c
)

set (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules")
set (TARGET less)

add_executable(${TARGET} ${SOURCES})

find_package(TERMCAP REQUIRED)
if (TERMCAP_FOUND)
  MESSAGE("TERMCAP Library found!")
  include_directories(${TERMCAP_INCLUDE_DIR})
  target_link_libraries (${TARGET} ${TERMCAP_LIBRARY})
endif(TERMCAP_FOUND)

target_link_libraries (${TARGET} curses)

# Config Variables
set (SECURE_COMPILE 0)
set (GNU_OPTIONS 1)
set (ONLY_RETURN 0)
set (LESKEYFILE \".less\")
set (MSDOS_COMPILER 0)
set (PATHNAME_SEP \"/\")
set (TGETENT_OK 1)
set (HAVE_SYS_TYPES_H 1)
set (HAVE_ERROR 1)
set (HAVE_TIME 1)
set (HAVE_SHELL 1)

set (DEF_METACHARS \";\ *?\\t\\n'\\\"\(\)<>[]|&^`\")
set (DEF_METAESCAPE \"\\\\\")

set (HAVE_DUP 1)
set (HAVE_MEMCPY 1)
set (HAVE_STRCHR 1)
set (HAVE_STRSTR 1)

configure_file("ProjectConfig.h.in" "ProjectConfig.h")
add_definitions(-DSYSDIR=\"/usr/local/etc\")

