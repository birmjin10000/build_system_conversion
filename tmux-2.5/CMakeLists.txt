cmake_minimum_required(VERSION 3.9)
project(myTMUX VERSION 2.5)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules")
set(TARGET tmux)

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

set(tmux_SOURCES alerts.c arguments.c attributes.c cfg.c client.c cmd-attach-session.c cmd-bind-key.c \\
  cmd-break-pane.c cmd-capture-pane.c cmd-choose-buffer.c cmd-choose-client.c cmd-choose-tree.c \\
  cmd-command-prompt.c cmd-confirm-before.c cmd-copy-mode.c cmd-detach-client.c cmd-display-message.c \\
  cmd-display-panes.c cmd-find-window.c cmd-find.c cmd-if-shell.c cmd-join-pane.c cmd-kill-pane.c \\
  cmd-kill-server.c cmd-kill-session.c cmd-kill-window.c cmd-list-buffers.c cmd-list-clients.c \\
  cmd-list-keys.c cmd-list-panes.c cmd-list-sessions.c cmd-list-windows.c cmd-list.c cmd-load-buffer.c \\
  cmd-lock-server.c cmd-move-window.c cmd-new-session.c cmd-new-window.c cmd-paste-buffer.c \\
  cmd-pipe-pane.c cmd-queue.c cmd-refresh-client.c cmd-rename-session.c cmd-rename-window.c \\
  cmd-resize-pane.c cmd-respawn-pane.c cmd-respawn-window.c cmd-rotate-window.c cmd-run-shell.c \\
  cmd-save-buffer.c cmd-select-layout.c cmd-select-pane.c cmd-select-window.c cmd-send-keys.c \\
  cmd-set-buffer.c cmd-set-environment.c cmd-set-hook.c cmd-set-option.c cmd-show-environment.c \\
  cmd-show-messages.c cmd-show-options.c cmd-source-file.c cmd-split-window.c cmd-string.c cmd-swap-pane.c \\
  cmd-swap-window.c cmd-switch-client.c cmd-unbind-key.c cmd-wait-for.c cmd.c colour.c compat.h \\
  control-notify.c control.c environ.c format.c grid-view.c grid.c hooks.c input-keys.c input.c job.c \\
  key-bindings.c key-string.c layout-custom.c layout-set.c layout.c log.c names.c notify.c options-table.c \\
  options.c paste.c proc.c resize.c screen-redraw.c screen-write.c screen.c server-client.c server-fn.c \\
  server.c session.c signal.c status.c style.c tmux.c tmux.h tty-acs.c tty-keys.c tty-term.c tty.c utf8.c \\
  window-choose.c window-clock.c window-copy.c window.c xmalloc.c xmalloc.h xterm-keys.c)

add_executable(${TARGET} ${tmux_SOURCES})

INCLUDE(CheckSymbolExists)
INCLUDE (CheckIncludeFile)
INCLUDE(CheckFunctionExists)
INCLUDE(CheckLibraryExists)

# Check for various headers. Alternatives included from compat.h.
CHECK_INCLUDE_FILE(bitstring.h HAVE_BITSTRING_H)
CHECK_INCLUDE_FILE(dirent.h HAVE_DIRENT_H)
CHECK_INCLUDE_FILE(fcntl.h HAVE_FCNTL_H)
CHECK_INCLUDE_FILE(inttypes.h HAVE_INTTYPES_H)
CHECK_INCLUDE_FILE(libutil.h HAVE_LIBUTIL_H)
CHECK_INCLUDE_FILE(ndir.h HAVE_NDIR_H)
CHECK_INCLUDE_FILE(paths.h HAVE_PATHS_H)
CHECK_INCLUDE_FILE(pty.h HAVE_PTY_H)
CHECK_INCLUDE_FILE(stdint.h HAVE_STDINT_H)
CHECK_INCLUDE_FILE(sys/dir.h HAVE_SYS_DIR_H)
CHECK_INCLUDE_FILE(sys/ndir.h HAVE_SYS_NDIR_H)
CHECK_INCLUDE_FILE(sys/tree.h HAVE_SYS_TREE_H)
CHECK_INCLUDE_FILE(util.h HAVE_UTIL_H)

# Look for library needed for flock.

# Check for functions that are replaced or omitted.
CHECK_FUNCTION_EXISTS(dirfd HAVE_DIRFD)
CHECK_FUNCTION_EXISTS(flock HAVE_FLOCK)
CHECK_FUNCTION_EXISTS(prctl HAVE_PRCTL)
CHECK_FUNCTION_EXISTS(sysconf HAVE_SYSCONF)

SET(COMPAT_LIB_PATH "compat")

# Check for functions with a compatibility implementation.
CHECK_FUNCTION_EXISTS(asprintf HAVE_ASPRINTF)
if (NOT HAVE_ASPRINTF)
  MESSAGE("haha - HAVE_ASPRINTF is not defined")
else (NOT HAVE_ASPRINTF)
  target_compile_definitions(${TARGET} PRIVATE HAVE_ASPRINTF)
endif (NOT HAVE_ASPRINTF)

CHECK_FUNCTION_EXISTS(cfmakeraw HAVE_CFMAKERAW)
if (NOT HAVE_CFMAKERAW)
  MESSAGE("HAVE_CFMAKERAW is not defined")
endif (NOT HAVE_CFMAKERAW)

CHECK_FUNCTION_EXISTS(closefrom HAVE_CLOSEFROM)
if (NOT HAVE_CLOSEFROM)
  MESSAGE("hoho - HAVE_CLOSEFROM is not defined")
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/closefrom.c)
else (NOT HAVE_CLOSEFROM)
  target_compile_definitions(${TARGET} PRIVATE HAVE_CLOSEFROM)
endif (NOT HAVE_CLOSEFROM)

CHECK_FUNCTION_EXISTS(explicit_bzero HAVE_EXPLICIT_BZERO)
if (NOT HAVE_EXPLICIT_BZERO)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/explicit_bzero.c)
else (NOT HAVE_EXPLICIT_BZERO)
  target_compile_definitions(${TARGET} PRIVATE HAVE_EXPLICIT_BZERO)
endif (NOT HAVE_EXPLICIT_BZERO)

CHECK_FUNCTION_EXISTS(fgetln HAVE_FGETLN)
CHECK_FUNCTION_EXISTS(fparseln HAVE_FPARSELN)

CHECK_FUNCTION_EXISTS(freezero HAVE_FREEZERO)
if (NOT HAVE_FREEZERO)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/freezero.c)
else (NOT HAVE_FREEZERO)
  target_compile_definitions(${TARGET} PRIVATE HAVE_FREEZERO)
endif (NOT HAVE_FREEZERO)

CHECK_FUNCTION_EXISTS(getdtablecount HAVE_GETDTABLECOUNT)
CHECK_FUNCTION_EXISTS(getprogname HAVE_GETPROGNAME)
CHECK_FUNCTION_EXISTS(recallocarray HAVE_RECALLOCARRAY)
CHECK_FUNCTION_EXISTS(reallocarray HAVE_REALLOCARRAY)
CHECK_FUNCTION_EXISTS(setenv HAVE_SETENV)
CHECK_FUNCTION_EXISTS(setproctitle HAVE_SETPROCTITLE)
CHECK_FUNCTION_EXISTS(strcasestr HAVE_STRCASESTR)

CHECK_FUNCTION_EXISTS(strlcat HAVE_STRLCAT)
if (NOT HAVE_STRLCAT)
  MESSAGE ("HAVE_STRLCAT is not defined!")
else (NOT HAVE_STRLCAT)
  add_definitions(-DHAVE_STRLCAT=1)
endif (NOT HAVE_STRLCAT)

CHECK_FUNCTION_EXISTS(strlcpy HAVE_STRLCPY)
if (NOT HAVE_STRLCPY)
  MESSAGE("HAVE_STRLCPY is not defined!")
else (NOT HAVE_STRLCPY)
  add_definitions(-DHAVE_STRLCPY=1)
endif (NOT HAVE_STRLCPY)

CHECK_FUNCTION_EXISTS(strndup HAVE_STRNDUP)
CHECK_FUNCTION_EXISTS(strsep HAVE_STRSEP)

CHECK_FUNCTION_EXISTS(strtonum HAVE_STRTONUM)
if (NOT HAVE_STRTONUM)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/strtonum.c)
else (NOT HAVE_STRTONUM)
  add_definitions(-DHAVE_STRTONUM=1)
endif (NOT HAVE_STRTONUM)

CHECK_FUNCTION_EXISTS(strnlen HAVE_STRNLEN)

MESSAGE(${COMPAT_LIB_SOURCES})
add_library(COMPAT_LIB ${COMPAT_LIB_SOURCES})
target_link_libraries(${TARGET} ${COMPAT_LIB})

# Look for a suitable queue.h.
CHECK_INCLUDE_FILE("sys/queue.h" HAVE_QUEUE_H)
CHECK_INCLUDE_FILE("sys/tree.h" HAVE_TREE_H)

if (HAVE_QUEUE_H)
  MESSAGE(HAVE_QUEUE_H is defined!)
endif (HAVE_QUEUE_H)



find_package(LibEvent REQUIRED)
if (LibEvent_FOUND)
  MESSAGE(LIBEVENT library found!)
  include_directories(${LIBEVENT_INCLUDE_DIR})
  target_link_libraries(${TARGET} ${LIBEVENT_LIB})
endif (LibEvent_FOUND)

find_package(CURSES REQUIRED)
if (CURSES_FOUND)
  MESSAGE(CURSES library found!)
  include_directories(${CURSES_INCLUDE_DIRS})
  target_link_libraries (${TARGET} ${CURSES_LIBRARIES})
  ADD_DEFINITIONS(-DHAVE_NCURSES_H)
endif (CURSES_FOUND)

# Look for clock_gettime. Must come before event_init.
CHECK_LIBRARY_EXISTS(rt clock_gettime "time.h" HAVE_CLOCK_GETTIME)

if (NOT HAVE_CLOCK_GETTIME)
   set(CMAKE_EXTRA_INCLUDE_FILES time.h)
   CHECK_FUNCTION_EXISTS(clock_gettime HAVE_CLOCK_GETTIME)
   SET(CMAKE_EXTRA_INCLUDE_FILES)
endif()

# Look for utf8proc.
OPTION(UTF8PROC_ENABLE "use utf8proc if it is installed" OFF)

# preprocessor definitions
target_compile_definitions(${TARGET} PRIVATE TMUX_CONF="/usr/local/etc/tmux.conf")
target_compile_definitions(${TARGET} PRIVATE VERSION=${PROJECT_VERSION})

# compiler flags
target_compile_options(${TARGET} PRIVATE -std=gnu99 PRIVATE -O2)

