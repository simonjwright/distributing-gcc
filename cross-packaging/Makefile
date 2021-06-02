BUILD_ROOT = /Volumes/Miscellaneous1/arm
COMPILER = gcc-$(VERSION)
VERSION = 11.1.0
MACHINE = arm-eabi
TARGET_ROOT = /opt

PACKAGE=$(COMPILER)-$(MACHINE)-apple-darwin15.pkg

all: $(PWD)/product/$(PACKAGE)

package: $(PWD)/package/$(PACKAGE)
product: $(PWD)/product/$(PACKAGE)

scripts: postinstall-tmpl Makefile
	-mkdir scripts
	sed							\
	  -e "s|BUILT_PREFIX|$(BUILD_ROOT)/$(COMPILER)|g"	\
	  -e "s|PREFIX|$(TARGET_ROOT)/$(COMPILER)|g"		\
	  -e "s|MACHINE|$(MACHINE)|g"				\
	  -e "s|VERSION|$(VERSION)|g"				\
	  <postinstall-tmpl					\
	  >scripts/postinstall
	chmod +x scripts/postinstall

$(PWD)/package/$(PACKAGE): scripts
	rm -rf package
	mkdir package
	pkgbuild							\
	  --root $(BUILD_ROOT)/$(COMPILER)				\
	  --scripts $$PWD/scripts					\
	  --identifier gcc-arm-eabi-darwin			\
	  --version $(VERSION)						\
	  --install-location $(TARGET_ROOT)/$(COMPILER)			\
	  --ownership recommended					\
	  $@

$(PWD)/product/$(PACKAGE): \
 $(PWD)/package/$(COMPILER)-$(MACHINE).pkg distribution.xml resources/*/*
	find . -name \*~ | xargs rm
	rm -rf product
	mkdir product
	productbuild						\
	  --distribution distribution.xml			\
	  --package-path $$PWD/package				\
	  --resources $$PWD/resources				\
	  $@

clean:
	rm -rf scripts package product

.PHONY: clean scripts package product resources
