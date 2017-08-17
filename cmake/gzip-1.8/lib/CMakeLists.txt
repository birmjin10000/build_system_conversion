cmake_minimum_required(VERSION 3.9)

set(am_libgzip_a_SOURCES
  cloexec.c opendir-safer.c dirname-lgpl.c basename-lgpl.c stripslash.c exitfail.c creat-safer.c \\
  open-safer.c fd-hook.c filenamecat-lgpl.c freading.c gettime.c glthread/lock.c math.c openat-die.c \\
  openat-safer.c printf-frexp.c printf-frexpl.c save-cwd.c savedir.c stat-time.c statat.c \\
  glthread/threadlib.c timespec.c unistd.c dup-safer.c fd-safer.c pipe-safer.c utimens.c xmalloc.c \\
  xalloc-die.c xsize.c yesno.c
)

set(libgzip_a_DEPENDENCIES
  asnprintf.c calloc.c chdir-long.c error.c fclose.c fflush.c fpurge.c fseek.c fseeko.c fseterr.c fstatat.c \\
  getopt.c getopt1.c isnanf.c isnanl.c lstat.c mempcpy.c memrchr.c open.c openat.c openat-proc.c perror.c \\
  printf-args.c printf-parse.c stat.c strerror.c strerror-override.c strerror_r.c unlink.c unlinkat.c \\
  vasnprintf.c
)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR})

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

add_library(libgzip ${am_libgzip_a_SOURCES} ${libgzip_a_DEPENDENCIES})

get_target_property(mytarget_basename libgzip OUTPUT_NAME)
get_target_property(mytarget_suffix libgzip SUFFIX)
set(mytarget_filename ${mytarget_basename}${mytarget_suffix})
message(${mytarget_filename})

