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

include(CheckTypeSize)
include(CheckCSourceCompiles)
include(GNUInstallDirs)

add_executable(${TARGET} ${SOURCES})

# preprocessor definitions
target_compile_definitions(${TARGET} PRIVATE SYSDIR=${SYSCONFDIR})

find_package(TERMCAP REQUIRED)
if (TERMCAP_FOUND)
  MESSAGE("TERMCAP Library found!")
  include_directories(${TERMCAP_INCLUDE_DIR})
  target_link_libraries (${TARGET} ${TERMCAP_LIBRARY})
endif(TERMCAP_FOUND)

target_link_libraries (${TARGET} curses)

# Checks for header files.
CHECK_INCLUDE_FILE(ctype.h HAVE_CTYPE_H)
if (HAVE_CTYPE_H)
  add_definitions(-DHAVE_CTYPE_H=1)
endif(HAVE_CTYPE_H)

CHECK_INCLUDE_FILE(errno.h HAVE_ERRNO_H)
if (HAVE_ERRNO_H)
  add_definitions(-DHAVE_ERRNO_H=1)
endif (HAVE_ERRNO_H)

CHECK_INCLUDE_FILE(fcntl.h HAVE_FCNTL_H)
if (HAVE_FCNTL_H)
  add_definitions(-DHAVE_FCNTL_H=1)
endif(HAVE_FCNTL_H)

CHECK_INCLUDE_FILE(limits.h HAVE_LIMITS_H)
if (HAVE_LIMITS_H)
  add_definitions(-DHAVE_LIMITS_H=1)
endif(HAVE_LIMITS_H)

CHECK_INCLUDE_FILE(stdio.h HAVE_STDIO_H)
if (HAVE_STDIO_H)
  add_definitions(-DHAVE_STDIO_H=1)
endif(HAVE_STDIO_H)

CHECK_INCLUDE_FILE(stdlib.h HAVE_STDLIB_H)
if (HAVE_STDLIB_H)
  add_definitions(-DHAVE_STDLIB_H=1)
endif(HAVE_STDLIB_H)

CHECK_INCLUDE_FILE(string.h HAVE_STRING_H)
if (HAVE_STRING_H)
  add_definitions(-DHAVE_STRING_H=1)
endif(HAVE_STRING_H)

CHECK_INCLUDE_FILE(termcap.h HAVE_TERMCAP_H)
if (HAVE_TERMCAP_H)
  add_definitions(-DHAVE_TERMCAP_H=1)
endif(HAVE_TERMCAP_H)

CHECK_INCLUDE_FILE(termio.h HAVE_TERMIO_H)
if (HAVE_TERMIO_H)
  add_definitions(-DHAVE_TERMIO_H=1)
endif(HAVE_TERMIO_H)

CHECK_INCLUDE_FILE(termios.h HAVE_TERMIOS_H)
if (HAVE_TERMIOS_H)
  add_definitions(-DHAVE_TERMIOS_H=1)
endif(HAVE_TERMIOS_H)

CHECK_INCLUDE_FILE(time.h HAVE_TIME_H)
if (HAVE_TIME_H)
  add_definitions(-DHAVE_TIME_H=1)
endif(HAVE_TIME_H)

CHECK_INCLUDE_FILE(unistd.h HAVE_UNISTD_H)
if (HAVE_UNISTD_H)
  add_definitions(-DHAVE_UNISTD_H=1)
endif(HAVE_UNISTD_H)

CHECK_INCLUDE_FILE(values.h HAVE_VALUES_H)
if (HAVE_VALUES_H)
  add_definitions(-DHAVE_VALUES_H=1)
endif(HAVE_VALUES_H)

CHECK_INCLUDE_FILE(sys/ioctl.h HAVE_SYS_IOCTL_H)
if (HAVE_SYS_IOCTL_H)
  add_definitions(-DHAVE_SYS_IOCTL_H=1)
endif(HAVE_SYS_IOCTL_H)

CHECK_INCLUDE_FILE(sys/stream.h HAVE_SYS_STREAM_H)
if (HAVE_SYS_STREAM_H)
  add_definitions(-DHAVE_SYS_STREAM_H=1)
endif(HAVE_SYS_STREAM_H)

CHECK_INCLUDE_FILE(wctype.h HAVE_WCTYPE_H)
if (HAVE_WCTYPE_H)
  add_definitions(-DHAVE_WCTYPE_H=1)
endif(HAVE_WCTYPE_H)

# Checks for typedefs, structures, and compiler characteristics.
check_type_size("off_t" SIZEOF_OFF_T)
if(NOT HAVE_SIZEOF_OFF_T)
  # Set it to "long int" to match the behavior of AC_TYPE_OFF_T (autotools).
  set(off_t "long int")
endif(NOT HAVE_SIZEOF_OFF_T)

check_type_size("size_t" SIZEOF_SIZE_T)
if(SIZEOF_SIZE_T STREQUAL "")
  # size_t is a unsigned type in POSIX storing at least 0.
  # Set it to "unsigned int" to match the behavior of AC_TYPE_SIZE_T (autotools).
  set(size_t "unsigned int")
endif(SIZEOF_SIZE_T STREQUAL "")

CHECK_C_SOURCE_COMPILES("
  #if HAVE_STDIO_H
  #include <stdio.h>
  #endif
  #if HAVE_STRING_H
  #include <string.h>
  #endif
  #if HAVE_ERRNO_H
  #include <errno.h>
  #endif

  void main() {
    static char *x;
    x = strerror(0);
  }
  " HAVE_STRERROR)
if(HAVE_STRERROR)
  add_definitions(-DHAVE_STRERROR=1)
endif(HAVE_STRERROR)

CHECK_C_SOURCE_COMPILES("
  #include <locale.h>
  #include <ctype.h>
  #include <langinfo.h>

  void main() {
    setlocale(LC_CTYPE,\"\");
    isprint(0);
    iscntrl(0);
  }
  " HAVE_LOCALE)
if (HAVE_LOCALE)
  add_definitions(-DHAVE_LOCALE=1)
endif(HAVE_LOCALE)

# Checks for identifiers.
CHECK_C_SOURCE_COMPILES("
  void main() {
    void *foo = 0;
  }
  " HAVE_VOID)
MESSAGE("Value of HAVE_VOID: " HAVE_VOID)
if (HAVE_VOID)
  add_definitions(-DHAVE_VOID=1)
endif(HAVE_VOID)

# Checks for regular expression functions.
CHECK_C_SOURCE_COMPILES("
  #include <sys/types.h>
  #include <regex.h>
  main() {
    regex_t r;
    regmatch_t rm;
    char *text = \"xabcy\";

    if (regcomp(&r, \"abc\", 0)) exit(1);
    if (regexec(&r, text, 1, &rm, 0)) exit(1);

    #ifndef __WATCOMC__
    if (rm.rm_so != 1) exit(1); /* check for correct offset */
    #else
    if (rm.rm_sp != text + 1) exit(1); /* check for correct offset */
    #endif

    exit(0);
  }
  " have_posix_regex FAIL_REGEX 1)
MESSAGE("Value of have_posix_regex: " ${have_posix_regex})
if(have_posix_regex)
  add_definitions(-DHAVE_POSIX_REGCOMP=1)
else(have_posix_regex)
  CHECK_C_SOURCE_COMPILES("
    #include <sys/types.h>
    #include <regex.h>

    void main() {
      regex_t *r;
      regfree(r);
    }
  " have_posix_regex2)
  if (have_posix_regex2)
    add_definitions(-DHAVE_POSIX_REGCOMP=1)
  endif(have_posix_regex2)
endif(have_posix_regex)


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

#set (DEF_METACHARS \";\ *?\\t\\n'\\\"\(\)<>[]|&^`\")
#set (DEF_METAESCAPE \"\\\\\")

set (HAVE_DUP 1)
set (HAVE_MEMCPY 1)
set (HAVE_STRCHR 1)
set (HAVE_STRSTR 1)
set(RETSIGTYPE void)

configure_file("ProjectConfig.h.in" "ProjectConfig.h")
add_definitions(-DSYSDIR=\"/usr/local/etc\")

