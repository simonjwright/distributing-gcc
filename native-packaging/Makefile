BUILD_ROOT = /Volumes/Miscellaneous1/x86_64
COMPILER = gcc-$(VERSION)
VERSION = 11.1.0
MACHINE = x86_64-apple-darwin15
TARGET_ROOT = /opt

all: $(PWD)/product/$(COMPILER)-$(MACHINE).pkg

scripts: postinstall-tmpl
	rm -rf scripts
	mkdir scripts
	sed							\
	  -e "s|BUILT_PREFIX|$(BUILD_ROOT)/$(COMPILER)|g"	\
	  -e "s|PREFIX|$(TARGET_ROOT)/$(COMPILER)|g"		\
	  -e "s|MACHINE|$(MACHINE)|g"				\
	  -e "s|VERSION|$(VERSION)|g"				\
	  <postinstall-tmpl					\
	  >scripts/postinstall
	chmod +x scripts/postinstall

$(PWD)/package/$(COMPILER)-$(MACHINE).pkg: scripts
	rm -rf package
	mkdir package
	pkgbuild							\
	  --root $(BUILD_ROOT)/$(COMPILER)				\
	  --scripts $$PWD/scripts					\
	  --identifier gcc-x86_64-apple-darwin15			\
	  --version $(VERSION)						\
	  --install-location $(TARGET_ROOT)/$(COMPILER)			\
	  --ownership recommended					\
	  $@

$(PWD)/product/$(COMPILER)-$(MACHINE).pkg: \
 $(PWD)/package/$(COMPILER)-$(MACHINE).pkg distribution.xml resources/*/*
	find . -name \*~ | xargs rm
	rm -rf product
	mkdir product
	productbuild						\
	  --distribution distribution.xml			\
	  --package-path $$PWD/package				\
	  --resources $$PWD/resources				\
	  $@

.PHONY: scripts package product resources
