sbin_PROGRAMS=	ikectl

ikectl_SOURCES=	ikeca.c ikectl.c parser.c \
		../iked/log.c ../iked/util.c

LIBCOMPAT=	$(top_builddir)/openbsd-compat/libopenbsd-compat.a
LDADD=		$(LIBCOMPAT)
AM_CPPFLAGS=	-include $(top_srcdir)/openbsd-compat/includes.h \
		-I$(top_srcdir)/openbsd-compat \
		-I$(top_srcdir)/iked \
		-I$(srcdir) @CPPFLAGS@ @DEFS@
MANPAGES=	ikectl.8.out
MANPAGES_IN=	ikectl.8
EXTRA_DIST=	$(MANPAGES_IN) ikeca.cnf
CLEANFILES=	$(CONFIGFILES) $(MANPAGES)
PATHSUBS=	-e 's|/etc/iked/|$(sysconfdir)/iked/|g'			\
		-e 's|/usr/libexec|$(libexecdir)|g'			\
		-e 's|/var/run/iked.sock|$(sockdir)/iked.sock|g'
FIXPATHSCMD=	$(SED) $(PATHSUBS)

$(MANPAGES): $(MANPAGES_IN)
	if test "$(MANTYPE)" = "man"; then \
		$(FIXPATHSCMD) $(MANPAGES_IN) | \
		    $(AWK) -f $(top_srcdir)/mdoc2man.awk > $@; \
	else \
		$(FIXPATHSCMD) $(MANPAGES_IN) > $@; \
	fi

install-exec-hook: $(MANPAGES)
	$(MKDIR_P) $(DESTDIR)$(sysconfdir)/iked/
	$(MKDIR_P) $(DESTDIR)$(SSLDIR)
	$(MKDIR_P) $(DESTDIR)$(sbindir)
	$(MKDIR_P) $(DESTDIR)$(mandir)/$(mansubdir)8

	@if [ ! -f $(DESTDIR)$(sysconfdir)/ikeca.cnf ]; then \
		$(INSTALL) -m 644 ikeca.cnf $(DESTDIR)$(sysconfdir)/ikeca.cnf; \
	else \
		echo "$(DESTDIR)$(sysconfdir)/ikeca.cnf already exists, install will not overwrite"; \
	fi

	$(INSTALL) -m 644 ikectl.8.out $(DESTDIR)$(mandir)/$(mansubdir)8/ikectl.8
