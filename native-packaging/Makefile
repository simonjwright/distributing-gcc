BUILD_ROOT = /opt
COMPILER = gcc-$(RELEASE)
RELEASE = 12.1.0
VERSION = 1
ARCH = aarch64
MACHINE = $(ARCH)-apple-darwin21
TARGET_ROOT = /opt

all: $(PWD)/product/$(COMPILER)-$(MACHINE).pkg

$(PWD)/package/$(COMPILER)-$(MACHINE).pkg:
	rm -rf package
	mkdir package
	pkgbuild						\
	  --root $(BUILD_ROOT)/$(COMPILER)-$(ARCH)		\
	  --identifier $(COMPILER)-$(MACHINE)			\
	  --version $(VERSION)					\
	  --install-location $(TARGET_ROOT)/$(COMPILER)-$(ARCH)	\
	  --ownership recommended				\
	  $@

distribution.xml: distribution.xml-tmpl Makefile
	sed					\
	  -e "s|COMPILER|$(COMPILER)|g"		\
	  -e "s|MACHINE|$(MACHINE)|g"		\
	  -e "s|VERSION|$(VERSION)|g"		\
	  <$<					\
	  >$@

$(PWD)/product/$(COMPILER)-$(MACHINE).pkg: \
 $(PWD)/package/$(COMPILER)-$(MACHINE).pkg distribution.xml resources/*/*
	find . -name \*~ | xargs rm
	rm -rf product
	mkdir product
	productbuild				\
	  --distribution distribution.xml	\
	  --package-path $$PWD/package		\
	  --resources $$PWD/resources		\
	  $@

clean:
	rm -rf package product distribution.xml

.PHONY: all package product clean
