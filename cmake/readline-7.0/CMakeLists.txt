cmake_minimum_required(VERSION 3.9)
project(MyReadlineInCmake)

# The C code source files for this library.
set(SOURCES
  readline.c funmap.c keymaps.c vi_mode.c parens.c rltty.c complete.c bind.c isearch.c display.c signals.c \\
  util.c kill.c undo.c macro.c input.c callback.c terminal.c xmalloc.c xfree.c \\
  nls.c search.c savestring.c text.c misc.c compat.c
)
set (HIST_SRCS history.c histexpand.c histfile.c histsearch.c shell.c mbutil.c)
set (COLORS_SRCS colors.c parse-colors.c)
set (TILDE_SRCS tilde.c)

set (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${PROJECT_SOURCE_DIR}/cmake/modules")
include(helpers)
include(CheckCSourceCompiles)
include(GNUInstallDirs)
include(CheckTypeSize)
INCLUDE(CheckFunctionExists)
INCLUDE(CheckSymbolExists)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

link_directories(${CMAKE_INSTALL_LIBDIR})
message(${CMAKE_INSTALL_LIBDIR})
include_directories(${CMAKE_INSTALL_INCLUDEDIR}/readline)
message(${CMAKE_INSTALL_INCLUDEDIR})


set(TARGET readline)

add_library(${TARGET} ${SOURCES} ${HIST_SRCS} ${TILDE_SRCS} ${COLORS_SRCS})
add_library(history ${HIST_SRCS} xmalloc.c xfree.c)

#target_link_libraries(${TARGET} readline)

#add_definitions(-DHAVE_DIRENT_H=1)
set(HAVE_DIRENT_H 1)
set(STDC_HEADERS 1)

check_type_size("size_t" SIZEOF_SIZE_T)
message(${SIZEOF_SIZE_T})
if(NOT ${SIZEOF_SIZE_T})
  # size_t is a unsigned type in POSIX storing at least 0.
  # Set it to "unsigned int" to match the behavior of AC_TYPE_SIZE_T (autotools).
  message("haha: size_t!")
  set(size_t "unsigned int")
endif(NOT ${SIZEOF_SIZE_T})

ac_check_funcs("fcntl;kill;lstat;readlink;memmove;pselect;putenv;select;setenv;setlocale;strcasecmp;strpbrk;tcgetattr;isascii;isxdigit;getpwent;getpwnam;getpwuid;chown;strcoll;mbrlen;mbscasecmp;mbscmp;mbsnrtowcs;mbsrtowcs;wcrtomb;wcscoll;wcsdup;wcwidth;wctype;iswlower;iswupper;towlower;towupper;iswctype;vsnprintf")

CHECK_FUNCTION_EXISTS(mbrtowc HAVE_MBRTOWC)
if (HAVE_MBRTOWC)
  CHECK_TYPE_SIZE(mbstate_t MBSTATE_T)
endif(HAVE_MBRTOWC)

ac_check_headers("fcntl.h;unistd.h;stdlib.h;varagrs.h;stdarg.h;stdbool.h;string.h;strings.h;limits.h;locale.h;pwd.h;memory.h;termcap.h;termios.h;termio.h;sys/ioctl.h;sys/pte.h;sys/stream.h;sys/select.h;sys/file.h;libaudit.h;wctype.h;wchar.h;langinfo.h;mbstr.h")

OPTION(OPT_CURSES "" OFF)

# TERMCAP library
find_package(TERMCAP REQUIRED)
if (TERMCAP_FOUND)
  MESSAGE("TERMCAP Library found! ${TERMCAP_LIBRARY}")
  include_directories(${TERMCAP_INCLUDE_DIR})
  target_link_libraries (${TARGET} ${TERMCAP_LIBRARY})
endif(TERMCAP_FOUND)

# option parsing for optional features
OPTION(OPT_MULTIBYTE "" ON)
OPTION(OPT_STATIC_LIBS "" ON)
OPTION(OPT_SHARED_LIBS "" ON)

if(NOT ${OPT_MULTIBYTE})
  set(NO_MULTIBYTE_SUPPORT 1)
endif(NOT ${OPT_MULTIBYTE})

CHECK_C_SOURCE_COMPILES("
  #include <signal.h>

  void main() {
    sigset_t ss;
    struct sigaction sa;
    sigemptyset(&ss); sigsuspend(&ss);
    sigaction(SIGINT, &sa, (struct sigaction *) 0);
    sigprocmask(SIG_BLOCK, &ss, (sigset_t *) 0);
  }
  " HAVE_POSIX_SIGNALS)

CHECK_C_SOURCE_COMPILES("
  #include <sys/types.h>
  #include <signal.h>
  #ifdef signal
  #undef signal
  #endif
  #ifdef __cplusplus
  extern \"C\"
  #endif
  void (*signal ()) ();

  void main() {
    return;
  }
" VOID_SIGHANDLER)

CHECK_C_SOURCE_COMPILES("
  #include <langinfo.h>
  void main() {
    char* cs = nl_langinfo(CODESET);
  }
" HAVE_LANGINFO_CODESET)

CHECK_C_SOURCE_COMPILES("
  #include <wchar.h>
  void main() {
    wchar_t foo;
    foo = 0;
  }
" HAVE_WCHAR_T)

CHECK_C_SOURCE_COMPILES("
  #include <wctype.h>
  void main() {
    wctype_t foo;
    foo = 0;
  }
" HAVE_WCTYPE_T)

CHECK_C_SOURCE_COMPILES("
  #include <wctype.h>
  void main() {
    wint_t foo;
    foo = 0;
  }
" HAVE_WINT_T)

CHECK_C_SOURCE_COMPILES("
  #include <sys/types.h>
  #include <sys/ioctl.h>
  void main() {
    struct winsize x;
  }
" STRUCT_WINSIZE_IN_SYS_IOCTL)
if (NOT STRUCT_WINSIZE_IN_SYS_IOCTL)
  CHECK_C_SOURCE_COMPILES("
    #include <sys/types.h>
    #include <termios.h>
    void main() {
      struct winsize x;
    }
  " STRUCT_WINSIZE_IN_TERMIOS)
endif(NOT STRUCT_WINSIZE_IN_SYS_IOCTL)

CHECK_C_SOURCE_COMPILES("
  #include <sys/types.h>
  #include <sys/ioctl.h>
  void main() {
    int x = TIOCSTAT;
  }
" TIOCSTAT_IN_SYS_IOCTL)

CHECK_C_SOURCE_COMPILES("
  #include <sys/types.h>
  #include <sys/ioctl.h>
  void main() {
    int x = FIONREAD;
  }
  " FIONREAD_IN_SYS_IOCTL)

CHECK_C_SOURCE_COMPILES("
  #include <stdio.h>
  #include <sys/types.h>
  #ifdef HAVE_UNISTD_H
  # include <unistd.h>
  #endif /* HAVE_UNISTD_H */
  # include <dirent.h>
  void main() {
    struct dirent d;
    int z;
    z = d.d_ino;
  }
  " HAVE_STRUCT_DIRENT_D_INO)

CHECK_C_SOURCE_COMPILES("
  #include <stdio.h>
  #include <sys/types.h>
  #ifdef HAVE_UNISTD_H
  # include <unistd.h>
  #endif /* HAVE_UNISTD_H */
  # include <dirent.h>
  void main() {
    struct dirent d;
    int z;
    z = d.d_fileno;
  }
  " HAVE_STRUCT_DIRENT_D_FILENO)

CHECK_C_SOURCE_COMPILES("
  #ifdef HAVE_UNISTD_H
  #include <unistd.h>
  #endif
  #include <sys/types.h>
  #include <signal.h>
  #include <setjmp.h>

  main() {
    #if !defined (_POSIX_VERSION) || !defined (HAVE_POSIX_SIGNALS)
    exit (1);
    #else
    int code;
    sigset_t set, oset;
    sigjmp_buf xx;

    /* get the mask */
    sigemptyset(&set);
    sigemptyset(&oset);
    sigprocmask(SIG_BLOCK, (sigset_t *)NULL, &set);
    sigprocmask(SIG_BLOCK, (sigset_t *)NULL, &oset);

    /* save it */
    code = sigsetjmp(xx, 1);
    if (code)
      exit(0);	/* could get sigmask and compare to oset here. */

    /* change it */
    sigaddset(&set, SIGINT);
    sigprocmask(SIG_BLOCK, &set, (sigset_t *)NULL);

    /* and siglongjmp */
    siglongjmp(xx, 10);
    exit(1);
    #endif
  }
" HAVE_POSIX_SIGSETJMP)

#CHECK_C_SOURCE_COMPILES("
#  #ifdef HAVE_LOCALE_H
#  #include <locale.h>
#  #endif
#  #include <stdio.h>
#  #include <ctype.h>
#
#  main(c, v)
#  int	c;
#  char	*v[];
#  {
#  	char	*deflocale;
#  	unsigned char x;
#  	int	r1, r2;
#
#  #ifdef HAVE_SETLOCALE
#  	/* We take a shot here.  If that locale is not known, try the
#  	   system default.  We try this one because '\\342' (226) is
#  	   known to be a printable character in that locale. */
#  	deflocale = setlocale(LC_ALL, "en_US.ISO8859-1");
#  	if (deflocale == 0)
#  		deflocale = setlocale(LC_ALL, "");
#  #endif
#
#  	x = '\\342';
#  	r1 = isprint(x);
#  	x -= 128;
#  	r2 = isprint(x);
#  	exit (r1 == 0 || r2 == 0);
#  }
#
#" CTYPE_NON_ASCII)

configure_file("config.h.in" "${CMAKE_CURRENT_SOURCE_DIR}/config.h")
