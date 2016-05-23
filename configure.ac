AC_INIT([libndpi], [2.1.0])

AC_CONFIG_MACRO_DIR([m4])

AM_INIT_AUTOMAKE([foreign subdir-objects])

LT_INIT

AC_PROG_CC
AM_PROG_CC_C_O
AX_PTHREAD

if test -d ".git"; then :
     GIT_TAG=`git log -1 --format=%h`
     GIT_DATE=`git log -1 --format=%cd`
     #
     # On CentOS 6 `git rev-list HEAD --count` does not work
     # 
     #
     GIT_NUM=`git log --pretty=oneline | wc -l | tr -d '[[:space:]]'`
     GIT_RELEASE="${PACKAGE_VERSION}-${GIT_NUM}-${GIT_TAG}"
else
     GIT_RELEASE="${PACKAGE_VERSION}"
     GIT_DATE=`date`
fi

AC_DEFINE_UNQUOTED(NDPI_GIT_RELEASE, "${GIT_RELEASE}", [GIT Release])
AC_DEFINE_UNQUOTED(NDPI_GIT_DATE, "${GIT_DATE}", [Last GIT change])

AC_CHECK_HEADERS([netinet/in.h stdint.h stdlib.h string.h unistd.h])

PCAP_HOME=$HOME/PF_RING/userland

if test -d $PCAP_HOME; then :
     echo -n ""
else
     PCAP_HOME=`pwd`/../../PF_RING/userland
fi
SHORT_MACHINE=`uname -m | cut -b1-3`
if test $SHORT_MACHINE = "arm"; then
     LIBNUMA=""
else
     AC_CHECK_LIB([numa], [numa_available], [LIBNUMA="-lnuma"])
fi

if test -f $PCAP_HOME/libpcap/libpcap.a; then :
     echo "Using libpcap from $PCAP_HOME"
     PCAP_INC="-I $PCAP_HOME/libpcap"
     PCAP_LIB="$PCAP_HOME/libpcap/libpcap.a $PCAP_HOME/lib/libpfring.a $LIBNUMA `$PCAP_HOME/lib/pfring_config --libs`"

     AC_CHECK_LIB([rt], [clock_gettime],   [PCAP_LIB="$PCAP_LIB -lrt"])
     AC_CHECK_LIB([nl], [nl_handle_alloc], [PCAP_LIB="$PCAP_LIB -lnl"])
     # The dlopen() function is in libdl on GLIBC-based systems
     # and in the C library for *BSD systems
     AC_CHECK_LIB([dl], [dlopen, dlsym],   [DL_LIB="-ldl"],
                  [AC_CHECK_LIB([c], [dlopen, dlsym], [DL_LIB="-lc"],
                                [AC_MSG_ERROR([unable to find the dlopen(), dlsym() functions]) ]) ])
else
    AC_CHECK_LIB([pcap], [pcap_open_live], [PCAP_LIB="-lpcap"])

     if test $ac_cv_lib_pcap_pcap_open_live = "no"; then :
       echo ""
       echo "ERROR: Missing libpcap(-dev) library required to compile the example application"
       echo "ERROR: Please install it and try again"
       exit
   fi
fi

dnl> https://github.com/json-c/json-c
AC_ARG_ENABLE([json-c],
    AS_HELP_STRING([--disable-json-c], [Disable json-c support]))

AS_IF([test "x$enable_json_c" != "xno"], [
       PKG_CONFIG_PATH=/usr/local/share/pkgconfig:$PKG_CONFIG_PATH
       pkg-config --exists json-c
       AS_IF([test "$?" == "0"],
             [
              CFLAGS="$CFLAGS $(pkg-config --cflags json-c)"
              LDFLAGS="$LDFLAGS $(pkg-config --libs json-c)"
              AC_CHECK_LIB(json-c, json_object_new_object, AC_DEFINE_UNQUOTED(HAVE_JSON_C, 1, [The JSON-C library is present]))
             ],
             [
		JSONC_HOME="$HOME/json-c"
	          if test -d "$JSONC_HOME"; then :
		        CFLAGS="$CFLAGS -I $JSONC_HOME"
			LDFLAGS="$LDFLAGS $JSONC_HOME/.libs/libjson-c.a"
    		        AC_MSG_RESULT([Found json-c in $JSONC_HOME])
		 	AC_DEFINE_UNQUOTED(HAVE_JSON_C, 1, [The JSON-C library is present])
		  fi
	     ])
       ])

#    AX_CHECK_COMPILE_FLAG([-Wall], [CFLAGS="$CFLAGS -Wall"])
#    AX_CHECK_COMPILE_FLAG([-Wextra], [CFLAGS="$CFLAGS -Wextra"])
#    AX_CHECK_COMPILE_FLAG([-Werror], [CFLAGS="$CFLAGS -Werror"])
#    AX_CHECK_COMPILE_FLAG([-Wmissing-prototypes], [CFLAGS="$CFLAGS -Wmissing-prototypes"])
#    AX_CHECK_COMPILE_FLAG([-Wstrict-prototypes], [CFLAGS="$CFLAGS -Wstrict-prototypes"])
#    AX_CHECK_COMPILE_FLAG([-Wmissing-declarations], [CFLAGS="$CFLAGS -Wmissing-declarations"])
#    AX_CHECK_COMPILE_FLAG([-Wpointer-arith], [CFLAGS="$CFLAGS -Wpointer-arith"])
#    AX_CHECK_COMPILE_FLAG([-Wdeclaration-after-statement], [CFLAGS="$CFLAGS -Wdeclaration-after-statement"])
#    AX_CHECK_COMPILE_FLAG([-Wformat-security], [CFLAGS="$CFLAGS -Wformat-security"])
#    AX_CHECK_COMPILE_FLAG([-Wwrite-strings], [CFLAGS="$CFLAGS -Wwrite-strings"])
#    AX_CHECK_COMPILE_FLAG([-Wshadow], [CFLAGS="$CFLAGS -Wshadow"])
#    AX_CHECK_COMPILE_FLAG([-Winline], [CFLAGS="$CFLAGS -Winline"])
#    AX_CHECK_COMPILE_FLAG([-Wnested-externs], [CFLAGS="$CFLAGS -Wnested-externs"])
#    AX_CHECK_COMPILE_FLAG([-Wfloat-equal], [CFLAGS="$CFLAGS -Wfloat-equal"])
#    AX_CHECK_COMPILE_FLAG([-Wundef], [CFLAGS="$CFLAGS -Wundef"])
#    AX_CHECK_COMPILE_FLAG([-Wendif-labels], [CFLAGS="$CFLAGS -Wendif-labels"])
#    AX_CHECK_COMPILE_FLAG([-Wempty-body], [CFLAGS="$CFLAGS -Wempty-body"])
#    AX_CHECK_COMPILE_FLAG([-Wcast-align], [CFLAGS="$CFLAGS -Wcast-align"])
#    AX_CHECK_COMPILE_FLAG([-Wclobbered], [CFLAGS="$CFLAGS -Wclobbered"])
#    AX_CHECK_COMPILE_FLAG([-Wvla], [CFLAGS="$CFLAGS -Wvla"])
#    AX_CHECK_COMPILE_FLAG([-Wpragmas], [CFLAGS="$CFLAGS -Wpragmas"])
#    AX_CHECK_COMPILE_FLAG([-Wunreachable-code], [CFLAGS="$CFLAGS -Wunreachable-code"])
#    AX_CHECK_COMPILE_FLAG([-Waddress], [CFLAGS="$CFLAGS -Waddress"])
#    AX_CHECK_COMPILE_FLAG([-Wattributes], [CFLAGS="$CFLAGS -Wattributes"])
#    AX_CHECK_COMPILE_FLAG([-Wdiv-by-zero], [CFLAGS="$CFLAGS -Wdiv-by-zero"])
#    AX_CHECK_COMPILE_FLAG([-Wshorten-64-to-32], [CFLAGS="$CFLAGS -Wshorten-64-to-32"])

#    AX_CHECK_COMPILE_FLAG([-Wconversion], [CFLAGS="$CFLAGS -Wconversion"])
#    AX_CHECK_COMPILE_FLAG([-Wextended-offsetof], [CFLAGS="$CFLAGS -Wextended-offsetof"])
#    AX_CHECK_COMPILE_FLAG([-Wformat-nonliteral], [CFLAGS="$CFLAGS -Wformat-nonliteral"])
#    AX_CHECK_COMPILE_FLAG([-Wlanguage-extension-token], [CFLAGS="$CFLAGS -Wlanguage-extension-token"])
#    AX_CHECK_COMPILE_FLAG([-Wmissing-field-initializers], [CFLAGS="$CFLAGS -Wmissing-field-initializers"])
#    AX_CHECK_COMPILE_FLAG([-Wmissing-noreturn], [CFLAGS="$CFLAGS -Wmissing-noreturn"])
#    AX_CHECK_COMPILE_FLAG([-Wmissing-variable-declarations], [CFLAGS="$CFLAGS -Wmissing-variable-declarations"])
#    AX_CHECK_COMPILE_FLAG([-Wpadded], [CFLAGS="$CFLAGS -Wpadded"])
#    AX_CHECK_COMPILE_FLAG([-Wsign-conversion], [CFLAGS="$CFLAGS -Wsign-conversion"])
#    AX_CHECK_COMPILE_FLAG([-Wswitch-enum], [CFLAGS="$CFLAGS -Wswitch-enum"])
#    AX_CHECK_COMPILE_FLAG([-Wunreachable-code-break], [CFLAGS="$CFLAGS -Wunreachable-code-break"])
#    AX_CHECK_COMPILE_FLAG([-Wunused-macros], [CFLAGS="$CFLAGS -Wunused-macros"])
#    AX_CHECK_COMPILE_FLAG([-Wunused-parameter], [CFLAGS="$CFLAGS -Wunused-parameter"])
#    AX_CHECK_COMPILE_FLAG([-Wredundant-decls], [CFLAGS="$CFLAGS -Wredundant-decls"])
    # Only work with Clang for the moment
#    AX_CHECK_COMPILE_FLAG([-Wheader-guard], [CFLAGS="$CFLAGS -Wheader-guard"])
    AX_CHECK_COMPILE_FLAG([-Wdocumentation], [CFLAGS="$CFLAGS -Wdocumentation"])
    AX_CHECK_COMPILE_FLAG([-Wunused-const-variable], [CFLAGS="$CFLAGS -Wunused-const-variable"])

AC_CHECK_LIB(pthread, pthread_setaffinity_np, AC_DEFINE_UNQUOTED(HAVE_PTHREAD_SETAFFINITY_NP, 1, [libc has pthread_setaffinity_np]))

AC_CONFIG_FILES([Makefile src/lib/Makefile example/Makefile tests/Makefile libndpi.pc])
AC_CONFIG_HEADERS(config.h)
AC_SUBST(GIT_RELEASE)
AC_SUBST(SVN_DATE)
AC_SUBST(JSON_C_LIB)
AC_SUBST(PCAP_INC)
AC_SUBST(PCAP_LIB)
AC_SUBST(DL_LIB)
AC_SUBST(HAVE_PTHREAD_SETAFFINITY_NP)

AC_OUTPUT
