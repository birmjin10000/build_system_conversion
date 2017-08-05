cmake_minimum_required (VERSION 3.9)
project(mySCREEN)

set(CFILES screen.c ansi.c fileio.c mark.c misc.c resize.c socket.c search.c tty.c term.c window.c utmp.c \\
           loadav.c putenv.c help.c termcap.c input.c attacher.c pty.c process.c display.c comm.c \\
           kmapdef.c acls.c braille.c braille_tsi.c logfile.c layer.c sched.c teln.c nethack.c encoding.c \\
           canvas.c layout.c viewport.c list_display.c list_generic.c list_window.c)
set (TARGET screen)

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})
ADD_DEFINITIONS(-DGIT_REV=\"\")

add_executable(${TARGET} ${CFILES})

find_package(CURSES REQUIRED)
if (CURSES_FOUND)
  MESSAGE(CURSES library found!)
  include_directories(${CURSES_INCLUDE_DIRS})
  target_link_libraries (${TARGET} ${CURSES_LIBRARIES})
endif (CURSES_FOUND)

# preprocessor definitions
target_compile_definitions(${TARGET} PRIVATE ETCSCREENRC="/usr/etc/screenrc" SCREENENCODINGS="/usr/share/screen/utf8encodings")

