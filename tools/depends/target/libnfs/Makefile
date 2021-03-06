include ../../Makefile.include
DEPS= ../../Makefile.include Makefile

# lib name, version
LIBNAME=libnfs
VERSION=1.5.0
SOURCE=$(LIBNAME)-$(VERSION)
ARCHIVE=$(SOURCE).tar.gz

# configuration settings
CONFIGURE=./configure --prefix=$(PREFIX)

LIBDYLIB=$(PLATFORM)/lib/.libs/$(LIBNAME).a

CLEAN_FILES=$(ARCHIVE) $(PLATFORM)

all: .installed-$(PLATFORM)

$(TARBALLS_LOCATION)/$(ARCHIVE):
	cd $(TARBALLS_LOCATION); $(RETRIEVE_TOOL) $(RETRIEVE_TOOL_FLAGS) $(BASE_URL)/$(ARCHIVE)

$(PLATFORM): $(TARBALLS_LOCATION)/$(ARCHIVE) $(DEPS)
	-rm -rf $(PLATFORM); mkdir -p $(PLATFORM)
	cd $(PLATFORM); $(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	cd $(PLATFORM); ./bootstrap
	cd $(PLATFORM); patch -p0 < ../timeval.patch
	cd $(PLATFORM); $(CONFIGURE)

$(LIBDYLIB): $(PLATFORM)
	$(MAKE) -C $(PLATFORM)

.installed-$(PLATFORM): $(LIBDYLIB)
	$(MAKE) -C $(PLATFORM) install
ifeq ($(OS),android)
	rm -f $(PREFIX)/lib/libnfs.la $(PREFIX)/lib/libnfs.so $(PREFIX)/lib/libnfs.so.1
	mv -f $(PREFIX)/lib/libnfs.so.1.0.5 $(PREFIX)/lib/libnfs.so
	$(RPL) -e "libnfs.so.1" "libnfs.so\x00\x00" $(PREFIX)/lib/libnfs.so
	-$(READELF) --dynamic $(PREFIX)/lib/libnfs.so | grep ibrary
endif
	touch $@
clean:
	$(MAKE) -C $(PLATFORM) clean
	rm -r .installed-$(PLATFORM)
	rm -rf $(PLATFORM)

distclean::
	rm -rf $(PLATFORM) .installed-$(PLATFORM)
