cmake_minimum_required(VERSION 3.9)
project(myTMUX VERSION 2.5)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules")
set(TARGET tmux)

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

# Figure out the platform.
IF(${CMAKE_SYSTEM_NAME} MATCHES "AIX")
  SET(PLATFORM aix)
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  add_definitions(-DBROKEN_CMSG_FIRSTHDR)
  SET(PLATFORM darwin)
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  SET(PLATFORM linux)
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "FreeBSD")
  SET(PLATFORM freebsd)
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "NetBSD")
  SET(PLATFORM netbsd)
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "OpenBSD")
  SET(PLATFORM openbsd)
ELSE(${CMAKE_SYSTEM_NAME} MATCHES "OpenBSD")
  SET(PLATFORM unknown)
endif()

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

string(CONCAT nodist_tmux_SOURCES osdep- ${PLATFORM} .c)
MESSAGE(${nodist_tmux_SOURCES})
add_executable(${TARGET} ${tmux_SOURCES} ${nodist_tmux_SOURCES})

INCLUDE(CheckSymbolExists)
INCLUDE(CheckIncludeFile)
INCLUDE(CheckFunctionExists)
INCLUDE(CheckLibraryExists)
INCLUDE(CheckPrototypeDefinition)

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

CHECK_INCLUDE_FILE(resolv.h HAVE_RESOLV_H)
if (HAVE_RESOLV_H)
  MESSAGE("resolv.h is exist in this system.")
endif (HAVE_RESOLV_H)

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
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/asprintf.c)
else (NOT HAVE_ASPRINTF)
  add_definitions(-DHAVE_ASPRINTF)
endif (NOT HAVE_ASPRINTF)

CHECK_FUNCTION_EXISTS(cfmakeraw HAVE_CFMAKERAW)
if (NOT HAVE_CFMAKERAW)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/cfmakeraw.c)
else (NOT HAVE_CFMAKERAW)
  add_definitions(-DHAVE_CFMAKERAW)
endif (NOT HAVE_CFMAKERAW)

CHECK_FUNCTION_EXISTS(closefrom HAVE_CLOSEFROM)
if (NOT HAVE_CLOSEFROM)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/closefrom.c)
else (NOT HAVE_CLOSEFROM)
  add_definitions(-DHAVE_CLOSEFROM)
endif (NOT HAVE_CLOSEFROM)

CHECK_FUNCTION_EXISTS(explicit_bzero HAVE_EXPLICIT_BZERO)
if (NOT HAVE_EXPLICIT_BZERO)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/explicit_bzero.c)
else (NOT HAVE_EXPLICIT_BZERO)
  add_definitions(-DHAVE_EXPLICIT_BZERO)
endif (NOT HAVE_EXPLICIT_BZERO)

CHECK_FUNCTION_EXISTS(fgetln HAVE_FGETLN)
CHECK_FUNCTION_EXISTS(fparseln HAVE_FPARSELN)

CHECK_FUNCTION_EXISTS(freezero HAVE_FREEZERO)
if (NOT HAVE_FREEZERO)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/freezero.c)
else (NOT HAVE_FREEZERO)
  add_definitions(-DHAVE_FREEZERO)
endif (NOT HAVE_FREEZERO)

CHECK_FUNCTION_EXISTS(getdtablecount HAVE_GETDTABLECOUNT)
if (NOT HAVE_GETDTABLECOUNT)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/getdtablecount.c)
else (NOT HAVE_GETDTABLECOUNT)
  add_definitions(-DHAVE_GETDTABLECOUNT=1)
endif (NOT HAVE_GETDTABLECOUNT)

CHECK_FUNCTION_EXISTS(getprogname HAVE_GETPROGNAME)
CHECK_FUNCTION_EXISTS(recallocarray HAVE_RECALLOCARRAY)
if (NOT HAVE_RECALLOCARRAY)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/recallocarray.c)
else (NOT HAVE_RECALLOCARRAY)
  add_definitions(-DHAVE_RECALLOCARRAY=1)
endif (NOT HAVE_RECALLOCARRAY)

CHECK_FUNCTION_EXISTS(reallocarray HAVE_REALLOCARRAY)
if (NOT HAVE_REALLOCARRAY)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/reallocarray.c)
else (NOT HAVE_REALLOCARRAY)
  add_definitions(-DHAVE_REALLOCARRAY=1)
endif(NOT HAVE_REALLOCARRAY)

CHECK_FUNCTION_EXISTS(setenv HAVE_SETENV)

CHECK_FUNCTION_EXISTS(setproctitle HAVE_SETPROCTITLE)
if (NOT HAVE_SETPROCTITLE)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/setproctitle.c)
else (NOT HAVE_SETPROCTITLE)
  add_definitions(-DHAVE_SETPROCTITLE=1)
endif (NOT HAVE_SETPROCTITLE)

CHECK_FUNCTION_EXISTS(strcasestr HAVE_STRCASESTR)

CHECK_FUNCTION_EXISTS(strlcat HAVE_STRLCAT)
if (NOT HAVE_STRLCAT)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/strlcat.c)
else (NOT HAVE_STRLCAT)
  add_definitions(-DHAVE_STRLCAT=1)
endif (NOT HAVE_STRLCAT)

CHECK_FUNCTION_EXISTS(strlcpy HAVE_STRLCPY)
if (NOT HAVE_STRLCPY)
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/strlcpy.c)
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

# Check for b64_ntop. If we have b64_ntop, we assume b64_pton as well.
SET(found_b64_ntop "no")
CHECK_PROTOTYPE_DEFINITION(b64_ntop "b64_ntop(NULL, 0, NULL, 0)" "NULL" "sys/types.h;netinet/in.h;resolv.h" HAVE_B64_NTOP_PROTO_DEFINED)
if (HAVE_B64_NTOP_PROTO_DEFINED)
  SET(found_b64_ntop "yes")
else (HAVE_B64_NTOP_PROTO_DEFINED)
  CHECK_SYMBOL_EXISTS(b64_ntop "resolv.h;sys/types.h;netinet/in.h" B64_NTOP_DEFINED)
  if (B64_NTOP_DEFINED)
    SET(found_b64_ntop "yes")
  endif (B64_NTOP_DEFINED)
endif (HAVE_B64_NTOP_PROTO_DEFINED)

if ("${found_b64_ntop}" STREQUAL "yes")
  MESSAGE("b64_ntop function is already on system!")
  add_definitions(-DHAVE_B64_NTOP=1)
  find_library(LIBRESOLV_LIB resolv)
  target_link_libraries(${TARGET} ${LIBRESOLV_LIB})
else ("${found_b64_ntop}" STREQUAL "yes")
  MESSAGE("Not really!!")
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/base64.c)
endif ("${found_b64_ntop}" STREQUAL "yes")

# Look for imsg_init in libutil.
CHECK_FUNCTION_EXISTS(imsg_init IMSG_DEFINED)
if (IMSG_DEFINED)
  add_definitions(-DHAVE_IMSG=1)
else(IMSG_DEFINED)
  Find_Library(UTIL_LIB_PATH util)
  CHECK_LIBRARY_EXISTS(util imsg_init ${UTIL_LIB_PATH} FOUND_IMSG)
  if (${FOUND_IMSG})
    add_definitions(-DHAVE_IMSG=1)
  else(${FOUND_IMSG})
    LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/imsg.c ${COMPAT_LIB_PATH}/imsg-buffer.c)
  endif(${FOUND_IMSG})
endif(IMSG_DEFINED)


# Look for stravis, compat/{vis,unvis}.c used if missing.
SET(found_stravis "no")
CHECK_FUNCTION_EXISTS(stravis STRAVIS_DEFINED)
if (STRAVIS_DEFINED)
  SET(found_stravis "yes")
  MESSAGE("checking if stravis is broken...")
  FILE(STRINGS "vis.h" stravis_prototype REGEX "strnvis\(char \*, const char \*, size_t, int\)")
  MESSAGE(${stravis_prototype})
  if ("${stravis_prototype}" STREQUAL "")
    SET(found_stravis "no")
  else("${stravis_prototype}" STREQUAL "")
    CHECK_SYMBOL_EXISTS(VIS_DQ "stdlib.h;vis.h" VIS_DQ_DEFINED)
    if (NOT VIS_DQ_DEFINED)
      SET(found_stravis "no")
    endif(NOT VIS_DQ_DEFINED)
  endif("${stravis_prototype}" STREQUAL "")
endif(STRAVIS_DEFINED)

MESSAGE(${found_stravis})
if ("${found_stravis}" STREQUAL "yes")
  add_definitions(-DHAVE_VIS=1)
else("${found_stravis}" STREQUAL "yes")
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/vis.c ${COMPAT_LIB_PATH}/unvis.c)
endif("${found_stravis}" STREQUAL "yes")

# Look for getopt. glibc's getopt does not enforce argument order and the ways
# of making it do so are stupid, so just use our own instead.
SET(found_getopt "no")
CHECK_FUNCTION_EXISTS(getopt GETOPT_DEFINED)
if (GETOPT_DEFINED)
  SET(found_getopt "yes")
  MESSAGE("checking if getopt is suitable...")
  CHECK_SYMBOL_EXISTS(__GLIBC__ "features.h" GLIBC_DEFINED)
  if(GLIBC_DEFINED)
    SET(found_getopt "no")
  else(GLIBC_DEFINED)
    CHECK_SYMBOL_EXISTS(optarg "unistd.h" OPTARG_DEFINED)
    if(NOT OPTARG_DEFINED)
      MESSAGE("optarg is not defined!")
      SET(found_getopt "no")
    else(NOT OPTARG_DEFINED)
      CHECK_SYMBOL_EXISTS(optind "unistd.h" OPTIND_DEFINED)
      if(NOT OPTIND_DEFINED)
        SET(found_getopt "no")
      else(NOT OPTIND_DEFINED)
        CHECK_SYMBOL_EXISTS(optreset "unistd.h" OPTRESET_DEFINED)
        if(NOT OPTRESET_DEFINED)
          SET(found_getopt "no")
        endif(NOT OPTRESET_DEFINED)
      endif(NOT OPTIND_DEFINED)
    endif(NOT OPTARG_DEFINED)
  endif(GLIBC_DEFINED)
endif(GETOPT_DEFINED)

MESSAGE(${found_getopt})
if("${found_getopt}" STREQUAL "yes")
  add_definitions(-DHAVE_GETOPT=1)
else("${found_getopt}" STREQUAL "yes")
  LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/getopt.c)
endif("${found_getopt}" STREQUAL "yes")

# Look for fdforkpty and forkpty in libutil.
CHECK_FUNCTION_EXISTS(fdforkpty FDFORKPTY_DEFINED)
if (FDFORKPTY_DEFINED)
  add_definitions(-DHAVE_FDFORKPTY=1)
else(FDFORKPTY_DEFINED)
  Find_Library(UTIL_LIB_PATH util)
  CHECK_LIBRARY_EXISTS(util fdforkpty ${UTIL_LIB_PATH} FOUND_FDFORKPTY)
  if (${FOUND_FDFORKPTY})
    add_definitions(-DHAVE_FDFORKPTY=1)
  else(${FOUND_FDFORKPTY})
    LIST(APPEND COMPAT_LIB_SOURCES ${COMPAT_LIB_PATH}/fdforkpty.c)
  endif(${FOUND_FDFORKPTY})
endif(FDFORKPTY_DEFINED)

CHECK_FUNCTION_EXISTS(forkpty FORKPTY_DEFINED)
if (FORKPTY_DEFINED)
  add_definitions(-DHAVE_FORKPTY=1)
else(FORKPTY_DEFINED)
  Find_Library(UTIL_LIB_PATH util)
  CHECK_LIBRARY_EXISTS(util forkpty ${UTIL_LIB_PATH} FOUND_FORKPTY)
  if (${FOUND_FORKPTY})
    add_definitions(-DHAVE_FORKPTY=1)
  else(${FOUND_FORKPTY})
    STRING(CONCAT forkpty_source ${COMPAT_LIB_PATH}/forkpty- ${PLATFORM} .c)
    LIST(APPEND nodist_tmux_SOURCES ${forkpty_source})
  endif(${FOUND_FORKPTY})
endif(FORKPTY_DEFINED)


# Look for a suitable queue.h.
SET(found_queue_h "no")
CHECK_SYMBOL_EXISTS(TAILQ_CONCAT "sys/queue.h" TAILQ_CONCAT_DEFINED)
if (TAILQ_CONCAT_DEFINED)
  SET(found_queue_h "yes")
else (TAILQ_CONCAT_DEFINED)
  SET(found_queue_h "no")
endif (TAILQ_CONCAT_DEFINED)

CHECK_SYMBOL_EXISTS(TAILQ_PREV "sys/queue.h" TAILQ_PREV_DEFINED)
if (TAILQ_PREV_DEFINED)
  SET(found_queue.h "yes")
else (TAILQ_PREV_DEFINED)
  SET(found_queue.h "no")
endif(TAILQ_PREV_DEFINED)

CHECK_SYMBOL_EXISTS(TAILQ_REPLACE "sys/queue.h" TAILQ_REPLACE_DEFINED)
if (NOT TAILQ_REPLACE_DEFINED)
  SET(found_queue_h "no")
endif (NOT TAILQ_REPLACE_DEFINED)

if ("${found_queue_h}" STREQUAL "yes")
  add_definitions(-DHAVE_QUEUE_H)
endif ("${found_queue_h}" STREQUAL "yes")

# Look for prctl(PR_SET_NAME).
CHECK_SYMBOL_EXISTS(PR_SET_NAME "sys/prctl.h" PR_SET_NAME_DEFINED)
if (PR_SET_NAME_DEFINED)
  add_definitions(-DHAVE_PR_SET_NAME)
endif(PR_SET_NAME_DEFINED)

MESSAGE(${COMPAT_LIB_SOURCES})
TARGET_SOURCES(${TARGET} PRIVATE ${COMPAT_LIB_SOURCES})
#add_library(COMPAT_LIB ${COMPAT_LIB_SOURCES})
#target_link_libraries(${TARGET} ${COMPAT_LIB})


# preprocessor definitions
target_compile_definitions(${TARGET} PRIVATE TMUX_CONF="/usr/local/etc/tmux.conf")
target_compile_definitions(${TARGET} PRIVATE VERSION=${PROJECT_VERSION})

# compiler flags
target_compile_options(${TARGET} PRIVATE -std=gnu99 PRIVATE -O2)

