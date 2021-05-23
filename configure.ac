AC_PREREQ([2.69])
LT_PREREQ([2.4.6])
AC_INIT([telegram-cli],[1.4.0])
AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_MACRO_DIRS([m4])
AC_CONFIG_SRCDIR([config.h.in])
AC_CONFIG_HEADERS([config.h])

m4_include([ax_lua.m4])
m4_include([ax_python.m4])
m4_include([m4_ax_check_openssl.m4])
m4_include([m4_ax_check_zlib.m4])
m4_include([m4-ax_gcc_builtin.m4])

# Checks for programs.
AC_PROG_CC
LT_INIT

# Checks for libraries.
AC_SEARCH_LIBS([clock_gettime], [rt])
AC_SEARCH_LIBS([backtrace], [execinfo])
AC_CHECK_LIB([event], [event_base_new], [], [AC_MSG_ERROR([no libevent found])])

EVENT_VER=""

AC_CHECK_HEADER(event2/event.h, [AC_DEFINE([EVENT_V2], [1], [Use libevent v2])], [
  AC_CHECK_HEADER(event.h, [AC_DEFINE([EVENT_V1], [1], [Use libevent v1])], [AC_MSG_ERROR([no libevent found])])
  ])

EXTRA_LIBS=""

#     OPENSSL_INCLUDES to the include directives required
#     OPENSSL_LIBS to the -l directives required
#     OPENSSL_LDFLAGS to the -L or -R flags required

AC_ARG_ENABLE(openssl,[  --disable-openssl       disable OpenSSL, and don't link against it
                            (removes read support for *.pub files)],
  [
  if test "x${enableval}" = "xno"; then
    AC_DEFINE([TGL_AVOID_OPENSSL],[1],[avoid OpenSSL entirely, use libgcrypt instead (this can't read *.pub files, though.)])
    AC_CHECK_LIB([gcrypt], [gcry_mpi_snatch], [], [AC_MSG_ERROR(["Need libgcrypt >= 1.60"])])
  else
    # Don't be annoying, so don't inform the user about --disable-openssl
    AX_CHECK_OPENSSL(,[AC_MSG_ERROR([No openssl found.])])
  fi
  ],[
  AX_CHECK_OPENSSL(,[AC_MSG_ERROR([No openssl found. With --disable-openssl, libtgl will use libgcrypt instead.])])
  ])
AX_CHECK_ZLIB(, [AC_MSG_ERROR([No zlib found])])

AC_CHECK_LIB([readline], [rl_save_prompt], [ EXTRA_LIBS="${EXTRA_LIBS} -lreadline" ; ], [AC_MSG_ERROR([no libreadline found])])

AC_MSG_CHECKING([for libconfig])
AC_ARG_ENABLE(libconfig,[  --disable-libconfig     disable configuration file support
                            (control behavior with command-line flags/switches only)],[
  if test "x${enableval}" = "xno"; then
    AC_MSG_RESULT([disabled])
  else
    AC_MSG_RESULT([enabled])
    AC_CHECK_LIB([config],[config_init],[],AC_MSG_ERROR([No libconfig found. Try --disable-libconfig]))
  fi
  ], [AC_MSG_RESULT([enabled])
      AC_CHECK_LIB([config],[config_init],[],AC_MSG_ERROR([No libconfig found. Try --disable-libconfig]))
     ]
)

AC_ARG_ENABLE(extf,[  --disable-extf          disable extf queries],[
  if test "x${enableval}" = "xno"; then
    AC_DEFINE([DISABLE_EXTF],[1],[disable extf queries])
  fi
], [])

AC_MSG_CHECKING([for liblua])
AC_ARG_ENABLE(liblua,[  --disable-liblua        disable Lua scripting support],[
  if test "x${enableval}" = "xno"; then
    AC_MSG_RESULT([disabled])
  else
    AC_MSG_RESULT([enabled])
    AX_PROG_LUA([], [], [
      AX_LUA_HEADERS([],[AC_MSG_ERROR([No lua headers found. Try --disable-liblua])])
      AX_LUA_LIBS([],[AC_MSG_ERROR([No lua libs found. Try --disable-liblua])])
      [EXTRA_LIBS="${EXTRA_LIBS} ${LUA_LIB}" ; ]
      [CPPFLAGS="${CPPFLAGS} ${LUA_INCLUDE}" ; ]
      AC_DEFINE(USE_LUA,1,[use lua])
    ], [AC_MSG_ERROR([No lua found. Try --disable-liblua])]
    )
  fi
  ], [AC_MSG_RESULT([enabled])
      AX_PROG_LUA([], [], [
        AX_LUA_HEADERS([],[AC_MSG_ERROR([No lua headers found. Try --disable-liblua])])
        AX_LUA_LIBS([],[AC_MSG_ERROR([No lua libs found. Try --disable-liblua])])
        [EXTRA_LIBS="${EXTRA_LIBS} ${LUA_LIB}" ; ]
        [CPPFLAGS="${CPPFLAGS} ${LUA_INCLUDE}" ; ]
        AC_DEFINE(USE_LUA,1,[use lua])
      ], [AC_MSG_ERROR([No lua found. Try --disable-liblua])]
      )
    ]
)

AC_MSG_CHECKING([for python])
AC_ARG_ENABLE(python,[  --disable-python        disable extensible behavior via Python scripting],[
  if test "x${enableval}" = "xno"; then
    AC_MSG_RESULT([disabled])
  else
    AC_MSG_RESULT([enabled])
    AX_PYTHON()
    if test "x${PYTHON_FOUND}" = "xno"; then
      AC_MSG_ERROR([No supported python lib version found. Try --disable-python])
    else
      AC_SUBST([PYTHON_LIBS])
      AC_SUBST([PYTHON_CFLAGS])
      EXTRA_LIBS="${EXTRA_LIBS} -l${PYTHON_LIB}"
      CPPFLAGS="${CPPFLAGS} -I${PYTHON_INCLUDE_DIR}"
      AC_DEFINE(USE_PYTHON,1,[use python])
    fi
  fi
  ], [AC_MSG_RESULT([enabled])
      AX_PYTHON()
      AC_SUBST([PYTHON_LIBS])
      AC_SUBST([PYTHON_CFLAGS])
      EXTRA_LIBS="${EXTRA_LIBS} -l${PYTHON_LIB}"
      CPPFLAGS="${CPPFLAGS} -I${PYTHON_INCLUDE_DIR}"
      AC_DEFINE(USE_PYTHON,1,[use python])]
)

AC_MSG_CHECKING([for libjansson])
AC_ARG_ENABLE(json,[  --disable-json          disable JSON parsing],[
  if test "x${enableval}" = "xno"; then
    AC_MSG_RESULT([disabled])
  else
    AC_MSG_RESULT([enabled])
    AC_CHECK_LIB([jansson],[json_array_set_new],[],AC_MSG_ERROR([No libjansson found. Try --disable-json]))
    AC_DEFINE(USE_JSON,1,[use json])
  fi
  ], [AC_MSG_RESULT([enabled])
      AC_CHECK_LIB([jansson],[json_array_set_new],[],AC_MSG_ERROR([No libjansson found. Try --disable-json]))
      AC_DEFINE(USE_JSON,1,[use json])]
)

#check for custom prog name
AC_MSG_CHECKING([progname])
AC_ARG_WITH(progname,[  --with-progname=NAME    set a custom name for the daemon binary
                            (default NAME=telegram-cli)],[
  if test "x${with_progname}" = "xno"; then
    AC_MSG_RESULT([default])
  elif test "x${with_progname}" = "xyes"; then
    AC_MSG_RESULT([default])
  else
    AC_MSG_RESULT(["$with_progname"])
    AC_DEFINE_UNQUOTED([PROG_NAME], ["$with_progname"], [Use custom prog name])
  fi
  ], [AC_MSG_RESULT([default])]
)

AC_ARG_ENABLE(valgrind,[  --enable-valgrind       disable extra debugging support],[
  if test "x${enableval}" = "xyes"; then
    AC_CHECK_HEADER(valgrind/memcheck.h, [AC_DEFINE([VALGRIND_FIXES], [1], [fixed for correct valgrind work])], [])
  fi
  ], []
)

# Checks for header files.
AC_CHECK_HEADERS([execinfo.h fcntl.h malloc.h netdb.h stdlib.h string.h unistd.h arpa/inet.h mach/mach.h netinet/in.h sys/file.h sys/socket.h termios.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_SIZE_T
AC_TYPE_UID_T
AC_C_INLINE

# Checks for library functions.
AC_FUNC_MALLOC
AC_FUNC_REALLOC
AC_CHECK_FUNCS([alarm endpwent memset memmove mkdir select socket strdup strndup uname])

AX_GCC_BUILTIN(__builtin_bswap32)

AC_SUBST(EXTRA_LIBS)
AC_CONFIG_FILES([Makefile])
AC_OUTPUT
