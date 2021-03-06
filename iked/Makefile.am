sbin_PROGRAMS=		iked

iked_MAPS=		eap_map.c ikev2_map.c
iked_MAPS_IN=		eap.h ikev2.h
iked_SOURCES=		ca.c chap_ms.c config.c control.c crypto.c dh.c \
			eap.c iked.c ikev2.c ikev2_msg.c ikev2_pld.c \
			imsg_util.c log.c ocsp.c policy.c proc.c timer.c \
			util.c parse.y $(iked_MAPS)

libpfkey_a_SOURCES=	pfkey.c
libpfkey_a_CPPFLAGS=	-include $(top_srcdir)/openbsd-compat/includes.h \
			-I$(top_srcdir)/openbsd-compat \
			@PFKEY_CPPFLAGS@

libcurve25519_a_SOURCES=smult_curve25519_ref.c 
libcurve25519_a_CPPFLAGS=

noinst_LIBRARIES=	libpfkey.a libcurve25519.a

LIBCOMPAT=	$(top_builddir)/openbsd-compat/libopenbsd-compat.a
LDADD=		$(LIBCOMPAT) libpfkey.a libcurve25519.a
AM_CPPFLAGS=	-include $(top_srcdir)/openbsd-compat/includes.h \
		-I$(top_srcdir)/openbsd-compat -I$(srcdir) \
		@CPPFLAGS@ @DEFS@
MANPAGES=	iked.8.out iked.conf.5.out
MANPAGES_IN=	iked.8 iked.conf.5
CONFIGFILES=	iked.conf.out
CONFIGFILES_IN=	iked.conf
EXTRA_DIST=	$(CONFIGFILES_IN) $(MANPAGES_IN)
CLEANFILES=	$(iked_MAPS) parse.c $(CONFIGFILES) $(MANPAGES)
PATHSUBS=	-e 's|/etc/iked/|$(sysconfdir)/iked/|g'			\
		-e 's|/usr/libexec|$(libexecdir)|g'			\
		-e 's|/var/run/iked.sock|$(sockdir)/iked.sock|g'
FIXPATHSCMD=	$(SED) $(PATHSUBS)

$(iked_MAPS): genmap.sh $(iked_MAPS_IN)
	mapname=`echo $@ | sed 's/_map\.c$$//'`; \
	hdrname=$${mapname}.h; \
	$(SH) genmap.sh $${hdrname} $${mapname} > $@
	@touch $@

$(MANPAGES): $(MANPAGES_IN)
	if test "$(MANTYPE)" = "man"; then \
		$(FIXPATHSCMD) $(MANPAGES_IN) | \
		    $(AWK) -f $(top_srcdir)/mdoc2man.awk > $@; \
	else \
		$(FIXPATHSCMD) $(MANPAGES_IN) > $@; \
	fi

$(CONFIGFILES): $(CONFIGFILES_IN)
	conffile=`echo $@ | sed 's/.out$$//'`; \
	$(FIXPATHSCMD) $(srcdir)/$${conffile} > $@

install-exec-hook: $(CONFIGFILES) $(MANPAGES)
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/ca
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/certs
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/crls
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/pubkeys/fqdn
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/pubkeys/ufqdn
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/pubkeys/ipv4
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/pubkeys/ipv6
	$(INSTALL) -d -m 700 $(DESTDIR)$(sysconfdir)/iked/private
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)/iked/
	$(INSTALL) -d $(DESTDIR)$(sbindir)
	$(INSTALL) -d $(DESTDIR)$(mandir)/$(mansubdir)8
	$(INSTALL) -d $(DESTDIR)$(mandir)/$(mansubdir)5

	@if [ ! -f $(DESTDIR)$(sysconfdir)/iked.conf ]; then \
		$(INSTALL) -m 600 iked.conf.out $(DESTDIR)$(sysconfdir)/iked.conf; \
	else \
		echo "$(DESTDIR)$(sysconfdir)/iked.conf already exists, install will not overwrite"; \
	fi

	$(INSTALL) -m 644 iked.8.out		$(DESTDIR)$(mandir)/$(mansubdir)8/iked.8
	$(INSTALL) -m 644 iked.conf.5.out	$(DESTDIR)$(mandir)/$(mansubdir)5/iked.conf.5
