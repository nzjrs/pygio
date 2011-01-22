ACLOCAL_AMFLAGS = -I m4
AUTOMAKE_OPTIONS = 1.7

SUBDIRS = gio

PLATFORM_VERSION = 2.0

CLEANFILES =
EXTRA_DIST = 				\
	m4/as-ac-expand.m4		\
	m4/jhflags.m4			\
	m4/python.m4

BUILT_EXTRA_DIST = 		\
	ChangeLog

INCLUDES = $(PYTHON_INCLUDES) $(GLIB_CFLAGS)

.PHONY: ChangeLog

ChangeLog:
	@echo Creating $@
	@if test -d "$(srcdir)/.git"; then \
	  (GIT_DIR=$(top_srcdir)/.git ./missing --run git log PYGOBJECT_2_17_0^^.. --stat) | fmt --split-only > $@.tmp \
	  && mv -f $@.tmp $@ \
	  || ($(RM) $@.tmp; \
	      echo Failed to generate ChangeLog, your ChangeLog may be outdated >&2; \
	      (test -f $@ || echo git-log is required to generate this file >> $@)); \
	else \
	  test -f $@ || \
	  (echo A git checkout and git-log is required to generate ChangeLog >&2 && \
	  echo A git checkout and git-log is required to generate this file >> $@); \
	fi

