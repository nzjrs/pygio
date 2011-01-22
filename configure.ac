-*- mode: m4 -*-
AC_PREREQ(2.52)

m4_define(python_min_ver, 2.5.2)

dnl the pygio/pygobject version number
m4_define(pygio_major_version, 2)
m4_define(pygio_minor_version, 27)
m4_define(pygio_micro_version, 0)
m4_define(pygio_version, pygio_major_version.pygio_minor_version.pygio_micro_version)

dnl versions of packages we require ...
m4_define(glib_required_version, 2.24.0)
m4_define(gio_required_version, 2.24.0)
m4_define(giounix_required_version, 2.22.4)
m4_define(pygobject_required_version,  2.27.0)

AC_INIT(pygio, pygio_version,
        [http://bugzilla.gnome.org/enter_bug.cgi?product=pygio])
AC_SUBST(ACLOCAL_AMFLAGS, "-I m4 -I .")
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_SRCDIR([gio/giomodule.c])

AC_DEFINE(PYGIO_MAJOR_VERSION, pygio_major_version, [PyGtk major version])
AC_SUBST(PYGIO_MAJOR_VERSION, pygio_major_version)
AC_DEFINE(PYGIO_MINOR_VERSION, pygio_minor_version, [PyGtk minor version])
AC_SUBST(PYGIO_MINOR_VERSION, pygio_minor_version)
AC_DEFINE(PYGIO_MICRO_VERSION, pygio_micro_version, [PyGtk micro version])
AC_SUBST(PYGIO_MICRO_VERSION, pygio_micro_version)

AM_CONFIG_HEADER(config.h)

AM_INIT_AUTOMAKE
#AC_CHECK_FUNCS(bind_textdomain_codeset)

dnl put the ACLOCAL flags in the makefile
ACLOCAL="$ACLOCAL $ACLOCAL_FLAGS"

AC_CANONICAL_BUILD
AC_CANONICAL_HOST
AC_MSG_CHECKING([for some Win32 platform])
case "$host" in
  *-*-mingw*|*-*-cygwin*)
    platform_win32=yes
    ;;
  *)
    platform_win32=no
    ;;
esac
AC_MSG_RESULT([$platform_win32])
AM_CONDITIONAL(PLATFORM_WIN32, test "$platform_win32" = "yes")

AC_MSG_CHECKING([for native Win32])
case "$host" in
  *-*-mingw*)
    os_win32=yes
    ;;
  *)
    os_win32=no
    ;;
esac
AC_MSG_RESULT([$os_win32])
AM_CONDITIONAL(OS_WIN32, test "$os_win32" = "yes")

AC_DISABLE_STATIC

dnl XXXX hack to kill off all the libtool tags ...
dnl it isn't like we are using C++ or Fortran.
dnl (copied from libglade/configure.in)
m4_define([_LT_AC_TAGCONFIG],[])

AC_LIBTOOL_WIN32_DLL
AC_PROG_LIBTOOL
dnl when using libtool 2.x create libtool early, because it's used in configure
m4_ifdef([LT_OUTPUT], [LT_OUTPUT])

AM_PATH_PYTHON(python_min_ver)

AM_CHECK_PYTHON_HEADERS(,[AC_MSG_ERROR(could not find Python headers)])

####
# JOHN: Only used in glib
####
#AC_MSG_CHECKING([for PySignal_SetWakeupFd in Python.h])
#py_prefix=`$PYTHON -c "import sys; print sys.prefix"`
#old_CPPFLAGS=$CPPFLAGS
#CPPFLAGS="-Wall -Werror $PYTHON_INCLUDES"
#AC_TRY_COMPILE([#include <Python.h>],
#  [PySignal_SetWakeupFd(0);],
#  setwakeupfd_ok=yes,
#  setwakeupfd_ok=no)
#AC_MSG_RESULT($setwakeupfd_ok)
#if test "x$setwakeupfd_ok" != xno; then
#  AC_DEFINE(HAVE_PYSIGNAL_SETWAKEUPFD, 1,
#                [Define to 1 if PySignal_SetWakeupFd is available])
#fi
#CPPFLAGS=$old_CPPFLAGS

####
# JOHN: Only used in glib
####
#AC_ARG_ENABLE(thread,
#  AC_HELP_STRING([--disable-thread], [Disable pygtk threading support]),,
#  enable_thread=yes)
#
#AM_CHECK_PYMOD(thread,,,enable_thread=no)

####
# JOHN: Disable docs for now
####
#dnl Building documentation
#AC_ARG_ENABLE(docs,
#  AC_HELP_STRING([--enable-docs], [Enable documentation building]),enable_docs=$enableval,
#  enable_docs=no)
#if test "${enable_docs}" != no; then
#  dnl Check for xsltproc
#  AC_PATH_PROG([XSLTPROC], [xsltproc])
#  if test -z "$XSLTPROC"; then
#    enable_docs=no
#  fi
#fi
#
#AM_CONDITIONAL(ENABLE_DOCS, test x$enable_docs != xno)

####
# JOHN: Only used in glib
####
#AC_MSG_CHECKING(whether to enable threading in pygtk)
#if test "x$enable_thread" != xno; then
#  extra_mods=gthread
#  THREADING_CFLAGS=
#  AC_MSG_RESULT(yes)
#else
#  extra_mods=
#  THREADING_CFLAGS="-DDISABLE_THREADING"
#  AC_MSG_RESULT(no)
#fi
#AC_SUBST(THREADING_CFLAGS)
#CPPFLAGS="${CPPFLAGS} $THREADING_CFLAGS"

dnl get rid of the -export-dynamic stuff from the configure flags ...
export_dynamic=`(./libtool --config; echo eval echo \\$export_dynamic_flag_spec) | sh`

dnl glib
AM_PATH_GLIB_2_0(glib_required_version,,[AC_MSG_ERROR(gobject is required to build pygio)],gobject $extra_mods)
if test -n "$export_dynamic"; then
  GLIB_LIBS=`echo $GLIB_LIBS | sed -e "s/$export_dynamic//"`
fi

dnl pygobject
PKG_CHECK_MODULES(PYGOBJECT, [pygobject-2.0 >= pygobject_required_version])

PYGOBJECT_INCLUDEDIR=`$PKG_CONFIG --variable=pygtkincludedir pygobject-2.0`
AC_SUBST(PYGOBJECT_INCLUDEDIR)

PYGOBJECT_DATADIR=`$PKG_CONFIG --variable=datadir pygobject-2.0`
AC_SUBST(PYGOBJECT_DATADIR)

dnl PyGObject defs
AC_SUBST(PYGOBJECT_DEFSDIR)
PYGOBJECT_DEFSDIR=`$PKG_CONFIG --variable defsdir pygobject-2.0`

dnl codegen from PyGObject
AC_SUBST([CODEGENDIR])

AC_MSG_CHECKING([for codegen])
CODEGENDIR=`$PKG_CONFIG --variable codegendir pygobject-2.0`

if test -z $CODEGENDIR; then
  AC_MSG_ERROR([unable to find PyGObject codegen])
else
  AC_MSG_RESULT($CODEGENDIR)
fi

dnl gio
PKG_CHECK_MODULES(GIO, gio-2.0 >= gio_required_version)
PKG_CHECK_MODULES(GIOUNIX, gio-unix-2.0 >= giounix_required_version,
	have_giounix=true, have_giounix=false)
AC_SUBST(GIOUNIX_CFLAGS)
AC_SUBST(GIOUNIX_LIBS)
AM_CONDITIONAL(BUILD_GIOUNIX, test $have_giounix = true)

####
# JOHN: IS AC_SUBST NEEDED?
####
#AC_SUBST(GIO_CFLAGS)
#AC_SUBST(GIO_LIBS)

#AC_MSG_CHECKING([for gio-types.defs])
#if test -n "$PYGOBJECT_DEFSDIR" && test -e "$PYGOBJECT_DEFSDIR/gio-types.defs"; then
#  AM_CONDITIONAL(HAVE_GIO_TYPES_DEFS, true)
#  AC_MSG_RESULT($PYGOBJECT_DEFSDIR/gio-types.defs)
#else
#  AM_CONDITIONAL(HAVE_GIO_TYPES_DEFS, false)
#  AC_MSG_RESULT(no)
#fi

####
# JOHN: Disable docs for now
####
#dnl Documentation
#PYGOBJECT_FIXXREF="$PYTHON `$PKG_CONFIG --variable=fixxref pygobject-2.0`"
#AC_SUBST(PYGOBJECT_FIXXREF)
#
#PYGOBJECT_PYGDOCS="`$PKG_CONFIG --variable=pygdocs pygobject-2.0`"
#AC_SUBST(PYGOBJECT_PYGDOCS)

dnl add required cflags ...
if test "x$GCC" = "xyes"; then
  JH_ADD_CFLAG([-Wall])
  JH_ADD_CFLAG([-fno-strict-aliasing])

  case $host_os in
  solaris*)
    ;;
  *)
    JH_ADD_CFLAG([-std=c9x])
    ;;
  esac
fi


AC_CONFIG_FILES(
  Makefile
  gio/Makefile)
AC_OUTPUT

echo
echo "giounix support: $have_giounix"
echo

