# **************************************************************************
# configure.ac for simage

m4_define([SIMAGE_MAJOR], [2])
m4_define([SIMAGE_MINOR], [0])
m4_define([SIMAGE_MICRO], [0])
m4_define([SIMAGE_BETA],  [a])
m4_define([SIMAGE_VERSION_STRING],
          [SIMAGE_MAJOR.SIMAGE_MINOR.SIMAGE_MICRO[]SIMAGE_BETA])

m4_define([SIMAGE_ABI_CURRENT], [m4_eval(SIMAGE_MAJOR*20+SIMAGE_MINOR)])
m4_define([SIMAGE_ABI_REVISION], [SIMAGE_MICRO])
m4_define([SIMAGE_ABI_AGE], [SIMAGE_MINOR])

# **************************************************************************
AC_INIT([simage], [SIMAGE_VERSION_STRING], [coin-bugs@coin3d.org])

AC_CONFIG_AUX_DIR([cfg])
AC_CONFIG_SRCDIR([src/simage.c])

# cygpath misbehaves with certain values in this variable
CYGWIN=

# **************************************************************************

AC_CANONICAL_TARGET

SIM_AC_SETUP_MSVCPP_IFELSE

if $BUILD_WITH_MSVC; then
  SIM_AC_SETUP_MSVCRT([
    CPPFLAGS="$CPPFLAGS $sim_ac_msvcrt_CPPFLAGS"
    CFLAGS="$CFLAGS $sim_ac_msvcrt_CFLAGS"
    CXXFLAGS="$CXXFLAGS $sim_ac_msvcrt_CXXFLAGS"
    LDFLAGS="$LDFLAGS $sim_ac_msvcrt_LDFLAGS"
    LIBS="$LIBS $sim_ac_msvcrt_LIBS"
  ])
fi

# **************************************************************************
# Check to see if the compiler supports the ANSI standard, and
# automatically add the necessary options if the compiler defaults
# to K&R C. If no ANSI mode is available, warn. (Don't terminate, as
# C++ compilers won't be strictly ANSI C compliant but most of them
# should still be able to compile the simage code.)

AC_PROG_CC
AM_PROG_CC_STDC
AC_PROG_CPP
AC_PROG_CXX
AC_PROG_CXXCPP
AC_LANG(C++)


# **************************************************************************
# Check to see if fink is installed on the system

SIM_AC_CHECK_FINK([
  CFLAGS="$CFLAGS -I/sw/include" # CPPFLAGS are set in macro, but we need CFLAGS too
  SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_fink_cppflags"
  SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_fink_ldflags"
])


sim_ac_qimage_support=false
AC_ARG_ENABLE(
  [qimage],
  AC_HELP_STRING([--enable-qimage], [Use Qt's QImage to load/save images]), [
  case $enableval in
    yes) sim_ac_qimage_support=true ;;
    *) ;;
  esac])

# only test for C++ compiler when qimage support is enabled

if test x"$am_cv_prog_cc_stdc" = x"no"; then
  AC_MSG_WARN([doesn't look like you're using an ANSI compatible C compiler])
fi
 
sim_ac_quicktime_support=false
case $host_os in
darwin*)
  sim_ac_quicktime_support=true
  AC_ARG_ENABLE(
    [quicktime],
    AC_HELP_STRING([--enable-quicktime], [Use Quicktime load/save images]), [
   case $enableval in
      no) sim_ac_quicktime_support=false ;;
      *) ;;
   esac])
  ;;
esac

# circumvent Automake 1.4h problem - explicitly use OBJEXT and EXEEXT
AC_OBJEXT
AC_EXEEXT

# **************************************************************************

AC_SUBST([SIMAGE_MAJOR_VERSION], [SIMAGE_MAJOR])
AC_SUBST([SIMAGE_MINOR_VERSION], [SIMAGE_MINOR])
AC_SUBST([SIMAGE_MICRO_VERSION], [SIMAGE_MICRO])
AC_SUBST([SIMAGE_VERSION], [SIMAGE_VERSION_STRING])
VERSION=$SIMAGE_VERSION

AC_SUBST([LT_CURRENT], [SIMAGE_ABI_CURRENT])
AC_SUBST([LT_REVISION], [SIMAGE_ABI_REVISION])
AC_SUBST([LT_AGE], [SIMAGE_ABI_AGE])

#  These are for include/simage.h.

AC_DEFINE_UNQUOTED([SIMAGE_MAJOR_VERSION], [$SIMAGE_MAJOR_VERSION],
  [define this to the simage major version number])
AC_DEFINE_UNQUOTED([SIMAGE_MINOR_VERSION], [$SIMAGE_MINOR_VERSION],
  [define this to the simage minor version number])
AC_DEFINE_UNQUOTED([SIMAGE_MICRO_VERSION], [$SIMAGE_MICRO_VERSION],
  [define this to the simage release version number])
AC_DEFINE_UNQUOTED([SIMAGE_VERSION], ["$SIMAGE_VERSION"],
  [define this to the simage major.minor.micro version string])

# **************************************************************************
# Miscellaneous options and initializations.

AM_INIT_AUTOMAKE([simage], [$VERSION])

# Default to not building a static library.
# Can be overridden by the user with --enable-static.
AM_DISABLE_STATIC

# Turn off default maintainer make-rules -- use ./autogen.sh instead.
AM_MAINTAINER_MODE

# Initialize libtool
if $BUILD_WITH_MSVC; then
  RANLIB=:
else
  AC_PROG_LIBTOOL
fi

case $host_os in
darwin6.* ) sim_ac_macosx_10_2=true ;;
* )         sim_ac_macosx_10_2=false ;;
esac
  
AM_CONDITIONAL([MACOSX_10_2], [$sim_ac_macosx_10_2])

# These are used for constructing the simage-config file.
AC_SUBST([SIMAGE_EXTRA_CPPFLAGS], [$CPPFLAGS])
AC_SUBST([SIMAGE_EXTRA_CFLAGS], [])
AC_SUBST([SIMAGE_EXTRA_LDFLAGS], [$LDFLAGS])
AC_SUBST([SIMAGE_EXTRA_LIBS], [$LIBS])

SIM_AC_COMPILE_DEBUG([
  CPPFLAGS="$CPPFLAGS -DSIMAGE_DEBUG=1"
], [
  CPPFLAGS="$CPPFLAGS -DSIMAGE_DEBUG=0"
])

SIM_AC_DEBUGSYMBOLS

# This is at least used to allow static compilation on Cygwin + MSWindows
# without hitting the SIMAGE_DLL / SIMAGE_NOT_DLL safety check.
CPPFLAGS="$CPPFLAGS -DSIMAGE_INTERNAL"

# **************************************************************************
# Want to configure an alternate installation?

AC_ARG_WITH(
  [alternate],
  [AC_HELP_STRING([--with-alternate=string],
                  [specify name of alternate configuration])],
  [CONFIG=$withval],
  [CONFIG=default])

AC_SUBST([CONFIG])

AC_ARG_WITH(
  [suffix],
  [AC_HELP_STRING([--with-suffix=string],
                  [specify library name suffix (e.g. "_g")])],
  [SUFFIX=$withval],
  [SUFFIX=])

AC_SUBST([SUFFIX])

# **************************************************************************
# general configuration queries

AC_CHECK_HEADERS([unistd.h])

# **************************************************************************
# libtiff and libpng uses math library functions.

SIM_AC_CHECK_MATHLIB(SIMAGE_EXTRA_LIBS="$SIMAGE_EXTRA_LIBS $sim_ac_libm",
                     AC_MSG_ERROR(could not find a way to use math library))

# **************************************************************************
# Check if qimage support is wanted (and if so, available)

if $sim_ac_qimage_support; then
  SIM_AC_CHECK_QT([
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_qt_cppflags"
    SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_qt_ldflags"
    SIMAGE_EXTRA_LIBS="$sim_ac_qt_libs $SIMAGE_EXTRA_LIBS"
    CPPFLAGS="$CPPFLAGS $sim_ac_qt_cppflags"
    LDFLAGS="$LDFLAGS $sim_ac_qt_ldflags"
    LIBS="$sim_ac_qt_libs $LIBS"
  ], [
    AC_MSG_WARN([can't compile and link against Qt development kit])
    sim_ac_qimage_support=false
  ])
fi

AM_CONDITIONAL(SIMAGE_QIMAGE_SUPPORT, $sim_ac_qimage_support)

if $sim_ac_qimage_support; then
  AC_DEFINE(SIMAGE_QIMAGE_SUPPORT, ,[define to support Qt's QImage])
  SIM_AC_CONFIGURATION_SETTING([QImage support], [Yes])
else
  SIM_AC_CONFIGURATION_SETTING([QImage support], [No (default)])
fi

# **************************************************************************
# Check if QuickTime support is wanted (and if so, available).

if $sim_ac_quicktime_support; then
  SIM_AC_HAVE_QUICKTIME_IFELSE([
    AC_DEFINE(SIMAGE_QUICKTIME_SUPPORT, , [define to support the QuickTime library])
    SIMAGE_EXTRA_LIBS="$SIMAGE_EXTRA_LIBS $sim_ac_quicktime_libs"
    LIBS="$LIBS $sim_ac_quicktime_libs"
    # These formats are guaranteed to be readable if we have
    # a QuickTime framework, so we do not have to test for them
    # individually. 
    SIM_AC_CONFIGURATION_SETTING([GIF image format support], 
                                 [Yes (QuickTime)])
    SIM_AC_CONFIGURATION_SETTING([JPEG image format support], 
                                 [Yes (QuickTime)])
    SIM_AC_CONFIGURATION_SETTING([PNG image format support], 
                                 [Yes (QuickTime)])
    SIM_AC_CONFIGURATION_SETTING([TIFF image format support], 
                                 [Yes (QuickTime)])
    SIM_AC_CONFIGURATION_SETTING([TGA image format support], 
                                 [Yes (QuickTime)])
    SIM_AC_CONFIGURATION_SETTING([BMP image format support], 
                                 [Yes (QuickTime)])
    SIM_AC_CONFIGURATION_SETTING([PSD image format support], 
                                 [Yes (QuickTime)])
  ], [
    SIM_AC_CONFIGURATION_SETTING([QuickTime support], [No])
  ])
  if test $"$sim_ac_have_quicktime" = xfalse; then
    sim_ac_quicktime_support=false
  fi
else
    SIM_AC_CONFIGURATION_SETTING([QuickTime support], [No])
fi

AM_CONDITIONAL(SIMAGE_QUICKTIME_SUPPORT, $sim_ac_quicktime_support)


if test x"$sim_ac_quicktime_support" = xfalse; then
  # **************************************************************************
  # Check if UNGIF support is wanted (and if so, available).

  SIM_AC_HAVE_LIBUNGIF_IFELSE([
    AC_DEFINE(HAVE_UNGIFLIB, , [define to support the ungif library])
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_libungif_cppflags"
    SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_libungif_ldflags"
    SIMAGE_EXTRA_LIBS="$sim_ac_libungif_libs $SIMAGE_EXTRA_LIBS"
    CPPFLAGS="$CPPFLAGS $sim_ac_libungif_cppflags"
    LDFLAGS="$LDFLAGS $sim_ac_libungif_ldflags"
    LIBS="$sim_ac_libungif_libs $LIBS"
    SIM_AC_CONFIGURATION_SETTING([GIF image format support], [Yes])
  ], [
    SIM_AC_CONFIGURATION_SETTING([GIF image format support], [No])
  ])

  # : ${sim_ac_have_libungif=false}
  # AM_CONDITIONAL(SIMAGE_UNGIF_SUPPORT, $sim_ac_have_libungif)

  # **************************************************************************
  # Check if JPEG support is wanted (and if so, available).

  SIM_AC_HAVE_LIBJPEG_IFELSE([
    AC_DEFINE(HAVE_JPEGLIB, , [define to support the jpeg library])
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_libjpeg_cppflags"
    SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_libjpeg_ldflags"
    SIMAGE_EXTRA_LIBS="$sim_ac_libjpeg_libs $SIMAGE_EXTRA_LIBS"
    CPPFLAGS="$CPPFLAGS $sim_ac_libjpeg_cppflags"
    LDFLAGS="$LDFLAGS $sim_ac_libjpeg_ldflags"
    LIBS="$sim_ac_libjpeg_libs $LIBS"
    SIM_AC_CONFIGURATION_SETTING([JPEG image format support], [Yes])
  ], [
    SIM_AC_CONFIGURATION_SETTING([JPEG image format support], [No])
  ])

  # : ${sim_ac_have_libjpeg=false}
  # AM_CONDITIONAL(SIMAGE_JPEG_SUPPORT, $sim_ac_have_libjpeg)

  # **************************************************************************

  SIM_AC_HAVE_LIBZLIB_IFELSE([
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_libzlib_cppflags"
    SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_libzlib_ldflags"
    SIMAGE_EXTRA_LIBS="$sim_ac_libzlib_libs $SIMAGE_EXTRA_LIBS"
    CPPFLAGS="$CPPFLAGS $sim_ac_libzlib_cppflags"
    LDFLAGS="$LDFLAGS $sim_ac_libzlib_ldflags"
    LIBS="$sim_ac_libzlib_libs $LIBS"
  ])
    
  # **************************************************************************
  # Check if PNG support is wanted (and if so, available).

  SIM_AC_HAVE_LIBPNG_IFELSE([
    AC_DEFINE(HAVE_PNGLIB, , [define for libpng support])
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_libpng_cppflags"
    SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_libpng_ldflags"
    SIMAGE_EXTRA_LIBS="$sim_ac_libpng_libs $SIMAGE_EXTRA_LIBS"
    CPPFLAGS="$CPPFLAGS $sim_ac_libpng_cppflags"
    LDFLAGS="$LDFLAGS $sim_ac_libpng_ldflags"
    LIBS="$sim_ac_libpng_libs $LIBS"
    SIM_AC_CONFIGURATION_SETTING([PNG image format support], [Yes])
  ], [
    SIM_AC_CONFIGURATION_SETTING([PNG image format support], [No])
    if test x"$sim_ac_have_libzlib" = xfalse; then
      SIM_AC_CONFIGURATION_WARNING(
        [PNG image format support may be disabled because of missing libz])
    fi
  ])

  # : ${sim_ac_have_libpng=false}
  # AM_CONDITIONAL(SIMAGE_PNG_SUPPORT, $sim_ac_have_libpng)

  # **************************************************************************
  # Check if TIFF support is wanted (and if so, available).

  SIM_AC_HAVE_LIBTIFF_IFELSE([
    AC_DEFINE(HAVE_TIFFLIB, , [define to support the tiff library])
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_libtiff_cppflags"
    SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_libtiff_ldflags"
    SIMAGE_EXTRA_LIBS="$sim_ac_libtiff_libs $SIMAGE_EXTRA_LIBS"
    CPPFLAGS="$CPPFLAGS $sim_ac_libtiff_cppflags"
    LDFLAGS="$LDFLAGS $sim_ac_libtiff_ldflags"
    LIBS="$sim_ac_libtiff_libs $LIBS"
    SIM_AC_CONFIGURATION_SETTING([TIFF image format support], [Yes])
  ], [
    SIM_AC_CONFIGURATION_SETTING([TIFF image format support], [No])
  ])

  # : ${sim_ac_have_libtiff=false}
  # AM_CONDITIONAL(SIMAGE_TIFF_SUPPORT, $sim_ac_have_libtiff)

  # **************************************************************************
  # Check if TARGA support is wanted.

  AC_ARG_WITH(
    [targa],
    [AC_HELP_STRING([--with-targa], [enable/disable support for Targa images])],
    [case $withval in
    yes | "") sim_ac_want_tga=true ;;
    no)       sim_ac_want_tga=false ;;
    *)        AC_MSG_ERROR([bad value "$withval" for --with-targa]) ;;
    esac],
    [sim_ac_want_tga=true])

  # AM_CONDITIONAL(SIMAGE_TGA_SUPPORT, $sim_ac_want_tga)
  if $sim_ac_want_tga; then
    AC_DEFINE(SIMAGE_TGA_SUPPORT, , [define to enable targa support])
    SIM_AC_CONFIGURATION_SETTING([TGA image format support], [Yes])
  else
    SIM_AC_CONFIGURATION_SETTING([TGA image format support], [No])
  fi
fi

# **************************************************************************
# Check if RGB support is wanted.

AC_ARG_WITH(
  [rgb],
  [AC_HELP_STRING([--with-rgb], [enable/disable support for RGB images])],
  [case $withval in
  yes | "") sim_ac_want_rgb=true ;;
  no)       sim_ac_want_rgb=false ;;
  *)        AC_MSG_ERROR([bad value "$withval" for --with-rgb]) ;;
  esac],
  [sim_ac_want_rgb=true])

# AM_CONDITIONAL(SIMAGE_RGB_SUPPORT, $sim_ac_want_rgb)
if $sim_ac_want_rgb; then
  AC_DEFINE(SIMAGE_RGB_SUPPORT, , [Define to enable rgb support])
  SIM_AC_CONFIGURATION_SETTING([RGB image format support], [Yes])
else
  SIM_AC_CONFIGURATION_SETTING([RGB image format support], [No])
fi

# **************************************************************************
# Check if PIC support is wanted.

AC_ARG_WITH(
  [pic],
  [AC_HELP_STRING([--with-pic], [enable/disable support for PIC images])],
  [case $withval in
  yes | "") sim_ac_want_pic=true ;;
  no)       sim_ac_want_pic=false ;;
  *)        AC_MSG_ERROR([bad value "$withval" for --with-pic]) ;;
  esac],
  [sim_ac_want_pic=true])

# AM_CONDITIONAL(SIMAGE_PIC_SUPPORT, $sim_ac_want_pic)
if $sim_ac_want_pic; then
  AC_DEFINE(SIMAGE_PIC_SUPPORT, , [define to enable pic support])
  SIM_AC_CONFIGURATION_SETTING([PIC image format support], [Yes])
else
  SIM_AC_CONFIGURATION_SETTING([PIC image format support], [No])
fi

# **************************************************************************
# Check if XWD support is wanted.

AC_ARG_WITH(
  [xwd],
  [AC_HELP_STRING([--with-xwd], [enable/disable support for XWD images])],
  [case $withval in
  yes | "") sim_ac_want_xwd=true ;;
  no)       sim_ac_want_xwd=false ;;
  *)        AC_MSG_ERROR([bad value "$withval" for --with-xwd]) ;;
  esac],
  [sim_ac_want_xwd=false])

# AM_CONDITIONAL(SIMAGE_XWD_SUPPORT, $sim_ac_want_xwd)
if $sim_ac_want_xwd; then
  AC_DEFINE(SIMAGE_XWD_SUPPORT, , [define to enable xwd support])
  SIM_AC_CONFIGURATION_SETTING([XWD support (experimental)], [Yes])
else
  SIM_AC_CONFIGURATION_SETTING([XWD support (experimental)], [No (default)])
fi

# **************************************************************************
# Check if EPS (encapsulated postscript) export is wanted.

AC_ARG_WITH(
  [eps],
  [AC_HELP_STRING([--with-eps], [enable/disable support for encapsulated postscript export])],
  [case $withval in
  yes | "") sim_ac_want_eps=true ;;
  no)       sim_ac_want_eps=false ;;
  *)        AC_MSG_ERROR([bad value "$withval" for --with-eps]) ;;
  esac],
  [sim_ac_want_eps=true])

# AM_CONDITIONAL(SIMAGE_EPS_SUPPORT, $sim_ac_want_eps)
if $sim_ac_want_eps; then
  AC_DEFINE(SIMAGE_EPS_SUPPORT, , [define to enable eps support])
  SIM_AC_CONFIGURATION_SETTING([Encapsulated postscript export support], [Yes])
else
  SIM_AC_CONFIGURATION_SETTING([Encapsulated postscript export support], [No])
fi

# **************************************************************************
# Check if mpeg2enc is wanted.

AC_ARG_WITH(
  [mpeg2enc],
  [AC_HELP_STRING([--with-mpeg2enc], [enable/disable support for mpeg2 encoding])],
  [case $withval in
  yes | "") sim_ac_want_mpeg2enc=true ;;
  no)       sim_ac_want_mpeg2enc=false ;;
  *)        AC_MSG_ERROR([bad value "$withval" for --with-mpeg2enc]) ;;
  esac],
  [sim_ac_want_mpeg2enc=false])

AM_CONDITIONAL(SIMAGE_MPEG2ENC_SUPPORT, $sim_ac_want_mpeg2enc)
if $sim_ac_want_mpeg2enc; then
  AC_DEFINE(SIMAGE_MPEG2ENC_SUPPORT, , [define to enable mpeg2enc support])
  SIM_AC_CONFIGURATION_SETTING([Mpeg2 encoding support], [Yes])
else
  SIM_AC_CONFIGURATION_SETTING([Mpeg2 encoding support], [No (default)])
fi

# **************************************************************************
# Check if avienc is wanted.

AC_ARG_WITH(
  [avienc],
  [AC_HELP_STRING([--with-avienc], [enable/disable support for avi encoding (Win32 only)])],
  [case $withval in
  yes | "") sim_ac_want_avienc=true ;;
  no)       sim_ac_want_avienc=false ;;
  *)        AC_MSG_ERROR([bad value "$withval" for --with-avienc]) ;;
  esac],
  [sim_ac_want_avienc=false])

# **************************************************************************
# Check for VfW (Video for Windows) - used by the avi encoder
#
# fixme 20020920 thammer: Do proper library linking, not just the
#                         visual c++ pragma hack

AC_MSG_CHECKING([for Video for Windows library])
AC_TRY_LINK([
#include <windows.h>
#include <vfw.h>
#pragma comment(lib, "vfw32.lib")
], [
  (void)AVIFileInit();
], [
  AC_MSG_RESULT([yes])
  AC_DEFINE([HAVE_VFW],, [Define to use the Video for Windows library])
], [
  AC_MSG_RESULT([no])
  if $sim_ac_want_avienc; then
    AC_MSG_WARN([need Video for Windows for avi encoding])
    SIM_AC_CONFIGURATION_WARNING(
      [avi encoding has been disabled because of missing Video for Windows library])
    sim_ac_want_avienc=false
  fi
])

# **************************************************************************
# Set avi ancode define

# AM_CONDITIONAL(SIMAGE_AVIENC_SUPPORT, $sim_ac_want_avienc)
if $sim_ac_want_avienc; then
  AC_DEFINE(SIMAGE_AVIENC_SUPPORT, , [define to enable avienc support])
  SIM_AC_CONFIGURATION_SETTING([Avi encoding support], [Yes])
else
  SIM_AC_CONFIGURATION_SETTING([Avi encoding support], [No (default)])
fi

# **************************************************************************
# Check if oggvorbis is wanted.

SIM_AC_CHECK_OGGVORBIS([
  SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_oggvorbis_cppflags"
  SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_oggvorbis_ldflags"
  SIMAGE_EXTRA_LIBS="$sim_ac_oggvorbis_libs $SIMAGE_EXTRA_LIBS"
  CPPFLAGS="$CPPFLAGS $sim_ac_oggvorbis_cppflags"
  LDFLAGS="$LDFLAGS $sim_ac_oggvorbis_ldflags"
  LIBS="$sim_ac_oggvorbis_libs $LIBS"
  AC_DEFINE(SIMAGE_OGGVORBIS_SUPPORT, 1, [Ogg Vorbis extensions available])
  SIM_AC_CONFIGURATION_SETTING([Ogg Vorbis support], [Yes])
], [
  SIM_AC_CONFIGURATION_SETTING([Ogg Vorbis support], [No])
])

# **************************************************************************
# Check if libsndfile is wanted.

SIM_AC_CHECK_LIBSNDFILE([
  SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS $sim_ac_libsndfile_cppflags"
  SIMAGE_EXTRA_LDFLAGS="$SIMAGE_EXTRA_LDFLAGS $sim_ac_libsndfile_ldflags"
  SIMAGE_EXTRA_LIBS="$sim_ac_libsndfile_libs $SIMAGE_EXTRA_LIBS"
  CPPFLAGS="$CPPFLAGS $sim_ac_libsndfile_cppflags"
  LDFLAGS="$LDFLAGS $sim_ac_libsndfile_ldflags"
  LIBS="$sim_ac_libsndfile_libs $LIBS"
  AC_DEFINE(SIMAGE_LIBSNDFILE_SUPPORT, 1, [libsndfile available])
  SIM_AC_CONFIGURATION_SETTING([libsnd support], [Yes])
], [
  SIM_AC_CONFIGURATION_SETTING([libsnd support], [No])
])

# **************************************************************************
# check if we are compiling with debug symbols

SIMAGE_DEBUG_BUILD=false
test -n "`echo -- $CPPFLAGS $CFLAGS $CXXFLAGS | grep -- '-g\\>'`" &&
  SIMAGE_DEBUG_BUILD=true

AC_SUBST(SIMAGE_DEBUG_BUILD)
AM_CONDITIONAL(SIMAGE_DEBUG_BUILD, $SIMAGE_DEBUG_BUILD)

# **************************************************************************
# simage module for Guile

AC_CHECK_HEADERS([dlfcn.h],
  [sim_ac_have_dlfcn_h=true],
  [sim_ac_have_dlfcn_h=false])

HAVE_GUILE=false
if $sim_ac_have_dlfcn_h; then
  AC_PATH_PROG(GUILE_PROG, guile, false)
  if $GUILE_PROG -c "(quit)"; then
    AC_CHECK_HEADERS([libguile.h guile/gh.h],
      [sim_ac_have_guile_sdk=true],
      [sim_ac_have_guile_sdk=false])
    if $sim_ac_have_guile_sdk; then
      HAVE_GUILE=true
    fi
  fi
fi
if $HAVE_GUILE; then
  SIM_AC_CONFIGURATION_SETTING([Guile module], [Build])
else
  SIM_AC_CONFIGURATION_SETTING([Guile module], [Ignore])
fi
AM_CONDITIONAL(HAVE_GUILE, $HAVE_GUILE)

# **************************************************************************
# Remove redundant options from environment variable option lists.

SIM_AC_UNIQIFY_OPTION_LIST(CPPFLAGS, $CPPFLAGS)
SIM_AC_UNIQIFY_OPTION_LIST(LDFLAGS, $LDFLAGS)
SIM_AC_UNIQIFY_OPTION_LIST(LIBS, $LIBS)

SIM_AC_UNIQIFY_OPTION_LIST(SIMAGE_EXTRA_CPPFLAGS, [-I"$includedir" $SIMAGE_EXTRA_CPPFLAGS])
SIM_AC_UNIQIFY_OPTION_LIST(SIMAGE_EXTRA_LDFLAGS, [-L"$libdir" $SIMAGE_EXTRA_LDFLAGS])
SIM_AC_UNIQIFY_OPTION_LIST(SIMAGE_EXTRA_LIBS, [$SIMAGE_EXTRA_LIBS])

AC_SUBST(LDFLAGS)

# **************************************************************************
# Remove gcc system directories includes from the CPPFLAGS.

CPP_AC_SEARCH_ORDER_FILTER(CPPFLAGS, $CPPFLAGS)
CPP_AC_SEARCH_ORDER_FILTER(SIMAGE_EXTRA_CPPFLAGS, $SIMAGE_EXTRA_CPPFLAGS)

# **************************************************************************
# Section related to building with MS Visual C++ on Windows

case $enable_static in
true | yes)  SIMAGE_STATIC=true;;
*)           SIMAGE_STATIC=false;;
esac

case $enable_shared in
true | yes)  SIMAGE_SHARED=true;;
*)           SIMAGE_SHARED=false;;
esac

LIBFLAGS=

# AC_SUBST(SIMAGE_STATIC)
# AC_SUBST(SIMAGE_SHARED)
AC_SUBST([LIBFLAGS], [])

AM_CONDITIONAL(BUILD_WITH_MSVC, $BUILD_WITH_MSVC)

if $BUILD_WITH_MSVC; then
  rm -f vc60.pdb
  LIBFLAGS="$LIBFLAGS $sim_ac_msvcrt_LIBLDFLAGS"
  LIBS="$LIBS $sim_ac_msvcrt_LIBLIBS"
  if $SIMAGE_STATIC; then
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS -DSIMAGE_NOT_DLL"
  else
    CPPFLAGS="-DSIMAGE_MAKE_DLL $CPPFLAGS"
    SIMAGE_EXTRA_CPPFLAGS="$SIMAGE_EXTRA_CPPFLAGS -DSIMAGE_DLL"
  fi

  debugfile=`pwd`/src/simage${SIMAGE_MAJOR_VERSION}${SUFFIX}.pdb
  debugfile=`cygpath -w "$debugfile" | sed 's,\\\\,\\\\\\\\,g'`
  if $SIMAGE_STATIC; then
    LIBFLAGS="-LIB $LIBFLAGS"
    LIBFLAGS="$LIBFLAGS /OUT:simage$SIMAGE_MAJOR_VERSION$SUFFIX.lib"
    SIM_AC_CONFIGURATION_SETTING([Simage Build Type], [Static .LIB])
  else
    LIBFLAGS="-DLL /INCREMENTAL:NO $LIBFLAGS"
    case $enable_symbols in
    no | false)
      LIBFLAGS="$LIBFLAGS /RELEASE"
      SIM_AC_CONFIGURATION_SETTING([Simage Build Type], [Dynamic .DLL])
      ;;
    *)
      CFLAGS="/Fd$debugfile -g $CFLAGS"
      LIBFLAGS="$LIBFLAGS /DEBUG /PDB:simage$SIMAGE_MAJOR_VERSION$SUFFIX.pdb"
      SIM_AC_CONFIGURATION_SETTING([Simage Build Type], [Dynamic .DLL + .PDB])
      ;;
    esac
    LIBFLAGS="$LIBFLAGS /OUT:simage$SIMAGE_MAJOR_VERSION$SUFFIX.dll"
  fi
  SIM_AC_CONFIGURATION_SETTING([C Library Version], [$sim_ac_msvcrt])

  # We use a version suffix on the .dll-file, so several incompatible
  # (major) versions can be installed on a system.
  #
  # BTW, when linking DLLs, the 3rd-party .lib files will be
  # linked into the .dll file. I believe it is still advisable to
  # list all libs used upon `simage-config --libs`, as we can then
  # also use them from "parent" code (remember that their interfaces
  # is not exposed from the DLL) without any fuss.

  if test -n "$SIMAGE_EXTRA_LIBS"; then
    SIMAGE_EXTRA_LIBS="-lsimage${SIMAGE_MAJOR_VERSION}${SUFFIX} $SIMAGE_EXTRA_LIBS"
  else
    SIMAGE_EXTRA_LIBS="-lsimage${SIMAGE_MAJOR_VERSION}${SUFFIX}"
  fi

  # These are for the .iwz InstallShield Express file under build/.
  win_includedir=`cygpath -w "$includedir"`
  AC_SUBST(win_includedir)
  win_libdir=`cygpath -w "$libdir"`
  AC_SUBST(win_libdir)

  # For the SuperPIMP (makensis) config file.
  win_srcdir=`cygpath -w "$srcdir"`
  AC_SUBST(win_srcdir)
  COIN_COMPILER=wrapmsvc
else
  SIMAGE_EXTRA_LIBS="-lsimage${SUFFIX} $SIMAGE_EXTRA_LIBS"

  # For the build/simage.spec RPM specification file.
  SIM_AC_ISO8601_DATE(simage_configure_date)
  AC_SUBST(simage_configure_date)
  COIN_COMPILER="$CC"
fi

AC_SUBST(COIN_COMPILER)
AC_SUBST([ac_compiler_gnu])

# **************************************************************************

AM_CONFIG_HEADER([config.h include/simage.h])

AC_CONFIG_FILES([
	Makefile
	src/Makefile
	guile/Makefile
        mpeg2enc/Makefile
	build/simage.spec
])

AC_OUTPUT

# **************************************************************************

SIM_AC_CONFIGURATION_SUMMARY

echo ""
echo "Done.  Now run make install to build $PACKAGE."
echo ""

# **************************************************************************