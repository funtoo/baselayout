# baselayout Makefile
# Copyright 2006-2008 Gentoo Foundation
# Distributed under the terms of the GNU General Public License v2
#
# The Makefile is deprecated and no longer used. Kept for reference only.

PKG = $(shell sed -n '/^\*/s:\*\([^ ]*\).*:\1:p' ChangeLog)

DESTDIR =
LIB = lib

INSTALL_DIR    = install -m 0755 -d
INSTALL_EXE    = install -m 0755
INSTALL_FILE   = install -m 0644
INSTALL_SECURE = install -m 0600

ifeq ($(OS),)
OS=$(shell uname -s)
ifneq ($(OS),Linux)
OS=BSD
endif
endif

KEEP_DIRS-Linux += /dev /sys
KEEP_DIRS = $(KEEP_DIRS-$(OS)) \
	/boot /home /mnt /root /proc /etc/profile.d \
	/usr/local/bin /usr/local/sbin /usr/local/share/doc /usr/local/share/man \
	/var/lock /var/run /var/empty

all:

clean:

install:
	# These dirs may not exist from prior versions
	for x in $(BASE_DIRS) ; do \
		$(INSTALL_DIR) $(DESTDIR)$$x || exit $$? ; \
		touch $(DESTDIR)$$x/.keep || exit $$? ; \
	done

	$(INSTALL_DIR) $(DESTDIR)/etc
	cp -pPR etc/* etc.$(OS)/* $(DESTDIR)/etc/
	$(INSTALL_DIR) $(DESTDIR)/usr/share/baselayout
	cp -pPR share.$(OS)/* $(DESTDIR)/usr/share/baselayout/

layout:
	# Create base filesytem layout
	for x in $(KEEP_DIRS) ; do \
		$(INSTALL_DIR) $(DESTDIR)$$x || exit $$? ; \
		touch $(DESTDIR)$$x/.keep || exit $$? ; \
	done
	# Special dirs
	install -m 0700 -d $(DESTDIR)/root || exit $$?
	touch $(DESTDIR)/root/.keep || exit $$?
	install -m 1777 -d $(DESTDIR)/var/tmp || exit $$?
	touch $(DESTDIR)/var/tmp/.keep || exit $$?
	install -m 1777 -d $(DESTDIR)/tmp || exit $$?
	touch $(DESTDIR)/tmp/.keep || exit $$?
	# FHS compatibility symlinks stuff
	ln -snf /var/tmp $(DESTDIR)/usr/tmp || exit $$?
	ln -snf share/man $(DESTDIR)/usr/local/man || exit $$?

diststatus:
	if test -d .svn ; then \
		svnfiles=`svn status 2>&1 | egrep -v '^(U|P)'` ; \
		if test "x$$svnfiles" != "x" ; then \
			echo "Refusing to package tarball until svn is in sync:" ; \
			echo "$$svnfiles" ; \
			echo "make distforce to force packaging" ; \
			exit 1 ; \
		fi \
	fi 

distlive:
	rm -rf /tmp/$(PKG)
	cp -r . /tmp/$(PKG)
	tar jcf /tmp/$(PKG).tar.bz2 -C /tmp $(PKG) --exclude=.svn
	rm -rf /tmp/$(PKG)
	ls -l /tmp/$(PKG).tar.bz2

distsvn:
	rm -rf /tmp/$(PKG)
	svn export -q . /tmp/$(PKG)
	tar jcf /tmp/$(PKG).tar.bz2 -C /tmp $(PKG)
	rm -rf /tmp/$(PKG)
	ls -l /tmp/$(PKG).tar.bz2

dist: diststatus distsvn

.PHONY: all clean install layout dist distforce diststatus

# vim: set ts=4 :
