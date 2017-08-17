COPTS = [
  '-DHAVE_CONFIG_H',
  '-O3',
  '-Wall',
]

cc_binary (
  name = "less",
  srcs = [
    "main.c", "screen.c", "brac.c", "ch.c", "charset.c", "cmdbuf.c", "command.c", "cvt.c", "decode.c",
    "edit.c", "filename.c", "forwback.c", "help.c", "ifile.c", "input.c", "jump.c", "line.c", "linenum.c",
    "lsystem.c", "mark.c", "optfunc.c", "option.c", "opttbl.c", "os.c", "output.c", "pattern.c", "position.c",
    "prompt.c", "search.c", "signal.c", "tags.c", "ttyin.c", "version.c", "less.h", "funcs.h", "option.h",
    "pattern.h", "charset.h", "cmd.h", "position.h", "lglob.h", "lesskey.h", "compose.inc", "ubin.inc",
    "wide.inc",
    ] + [":ProjectConfig"],
  deps = ["@ncurses//:ncurses"],
  copts = COPTS + [
    '-I.',  # For several header files in root directory
    '-I$(GENDIR)', # For ProjectConfig.h
  ],
  defines = [
    'SYSDIR=\\"/usr/local/\\"',
    'EDIT_PGM=\\"vi\\"', 'HAVE_CONST=1', 'HAVE_CTYPE_H=1', 'HAVE_ERRNO=1', 'HAVE_ERRNO_H=1', 'HAVE_FCHMOD=1',
    'HAVE_FCNTL_H=1', 'HAVE_FILENO=1', 'HAVE_FLOAT=1', 'HAVE_FSYNC=1', 'HAVE_INTTYPES_H=1', 'HAVE_LIMITS_H=1',
    'HAVE_LOCALE=1', 'HAVE_MEMORY_H=1', 'HAVE_OSPEED=1', 'HAVE_POPEN=1', 'HAVE_POSIX_REGCOMP=1',
    'HAVE_SIGEMPTYSET=1', 'HAVE_SIGPROCMASK=1', 'HAVE_SIGSETMASK=1', 'HAVE_SIGSET_T=1', 'HAVE_SNPRINTF=1',
    'HAVE_STAT=1', 'HAVE_STAT_INO=1', 'HAVE_STDINT_H=1', 'HAVE_STDIO_H=1', 'HAVE_STDLIB_H=1',
    'HAVE_STRERROR=1', 'HAVE_STRINGS_H=1', 'HAVE_STRING_H=1', 'HAVE_SYSTEM=1', 'HAVE_SYS_ERRLIST=1',
    'HAVE_SYS_IOCTL_H=1', 'HAVE_SYS_STAT_H=1', 'HAVE_SYS_TYPES_H=1', 'HAVE_TERMCAP_H=1',
    'HAVE_TERMIOS_FUNCS=1', 'HAVE_TERMIOS_H=1', 'HAVE_TIME_H=1', 'HAVE_TIME_T=1', 'HAVE_UNISTD_H=1',
    'HAVE_UPPER_LOWER=1', 'HAVE_VOID=1', 'HAVE_WCTYPE=1', 'HAVE_WCTYPE_H=1', 'PACKAGE_BUGREPORT=\\"\\"',
    'PACKAGE_NAME=\\"less\\"',
#    'PACKAGE_STRING=\\"less 1\\"',
    'PACKAGE_TARNAME=\\"less\\"',
    'PACKAGE_URL=\\"\\"', 'PACKAGE_VERSION=\\"1\\"', 'RETSIGTYPE=void', 'SECURE_COMPILE=0',
    'STDC_HEADERS=1', 'TIME_WITH_SYS_TIME=1',
  ]
)

genrule(
  name = 'ProjectConfig',
  outs = ['ProjectConfig.h'],
  cmd = """
cat > $@ <<"EOF"

/* User preferences.  */

/*
 * SECURE is 1 if you wish to disable a bunch of features in order to
 * be safe to run by unprivileged users.
 * SECURE_COMPILE is set by the --with-secure configure option.
 */
#define	SECURE		SECURE_COMPILE

/*
 * SHELL_ESCAPE is 1 if you wish to allow shell escapes.
 * (This is possible only if your system supplies the system() function.)
 */
#define	SHELL_ESCAPE	(!SECURE)

/*
 * EXAMINE is 1 if you wish to allow examining files by name from within less.
 */
#define	EXAMINE		(!SECURE)

/*
 * TAB_COMPLETE_FILENAME is 1 if you wish to allow the TAB key
 * to complete filenames at prompts.
 */
#define	TAB_COMPLETE_FILENAME	(!SECURE)

/*
 * CMD_HISTORY is 1 if you wish to allow keys to cycle through
 * previous commands at prompts.
 */
#define CMD_HISTORY 1

/*
 * HILITE_SEARCH is 1 if you wish to have search targets to be 
 * displayed in standout mode.
 */
#define HILITE_SEARCH 1

/*
 * EDITOR is 1 if you wish to allow editor invocation (the "v" command).
 * (This is possible only if your system supplies the system() function.)
 * EDIT_PGM is the name of the (default) editor to be invoked.
 */
#define	EDITOR		(!SECURE)

/*
 * TAGS is 1 if you wish to support tag files.
 */
#define	TAGS		(!SECURE)

/*
 * USERFILE is 1 if you wish to allow a .less file to specify 
 * user-defined key bindings.
 */
#define	USERFILE	(!SECURE)

/*
 * GLOB is 1 if you wish to have shell metacharacters expanded in filenames.
 * This will generally work if your system provides the "popen" function
 * and the "echo" shell command.
 */
#define	GLOB		(!SECURE)

/*
 * PIPEC is 1 if you wish to have the "|" command
 * which allows the user to pipe data into a shell command.
 */
#define	PIPEC		(!SECURE)

/*
 * LOGFILE is 1 if you wish to allow the -l option (to create log files).
 */
#define	LOGFILE		(!SECURE)

/*
 * GNU_OPTIONS is 1 if you wish to support the GNU-style command
 * line options --help and --version.
 */
#define GNU_OPTIONS 1

/*
 * ONLY_RETURN is 1 if you want RETURN to be the only input which
 * will continue past an error message.
 * Otherwise, any key will continue past an error message.
 */
#define ONLY_RETURN 0

/*
 * LESSKEYFILE is the filename of the default lesskey output file 
 * (in the HOME directory).
 * LESSKEYFILE_SYS is the filename of the system-wide lesskey output file.
 * DEF_LESSKEYINFILE is the filename of the default lesskey input 
 * (in the HOME directory).
 * LESSHISTFILE is the filename of the history file
 * (in the HOME directory).
 */
#define LESSKEYFILE ".less"
#define	LESSKEYFILE_SYS		SYSDIR "/sysless"
#define DEF_LESSKEYINFILE ".lesskey"
#define LESSHISTFILE ".lesshst"

/* Settings always true on Unix.  */

// Define MSDOS_COMPILER if compiling under Microsoft C.
#define MSDOS_COMPILER 0

// Pathname separator character.
#define PATHNAME_SEP "/"

/*
 * The value returned from tgetent on success.
 * Some HP-UX systems return 0 on success.
 */
#define TGETENT_OK 1

// HAVE_SYS_TYPES_H is 1 if your system has <sys/types.h>.
#define HAVE_SYS_TYPES_H 1

/*
 * HAVE_PERROR is 1 if your system has the perror() call.
 * (Actually, if it has sys_errlist, sys_nerr and errno.)
 */
#define HAVE_PERROR 1

// HAVE_TIME is 1 if your system has the time() call.
#define HAVE_TIME 1

// HAVE_SHELL is 1 if your system supports a SHELL command interpreter.
#define HAVE_SHELL 1

// Default shell metacharacters and meta-escape character.
#define	DEF_METACHARS	"; *?\\t\\n'\\"()<>[]|&^`#\\\\$$%=~"
#define	DEF_METAESCAPE	"\\\\"

// HAVE_DUP is 1 if your system has the dup() call.
#define HAVE_DUP 1

/* Define to 1 if you have the memcpy() function. */
#define HAVE_MEMCPY 1

/* Define to 1 if you have the strchr() function. */
#define HAVE_STRCHR 1

/* Define to 1 if you have the strstr() function. */
#define HAVE_STRSTR 1

// Sizes of various buffers.
#define CMDBUF_SIZE 2048
#define UNGOT_SIZE 200
#define LINEBUF_SIZE 1024
#define OUTBUF_SIZE 1024
#define PROMPT_SIZE 2048
#define TERMBUF_SIZE 2048
#define TERMSBUF_SIZE 2048
#define TAGLINE_SIZE 1024
#define TABSTOP_MAX 128
EOF""",
)
