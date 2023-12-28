BUILD_ROOT = /Volumes/Miscellaneous3/arm
BUILD_NOTES = https://github.com/simonjwright/building-gcc-macos-arm-eabi/releases/tag/gcc-13.2.0-aarch64
COMPILER = gcc-$(RELEASE)
RELEASE = 13.2.0
VERSION = 1
HOST = aarch64
ARCH = arm-eabi
MACHINE = $(ARCH)
TARGET_ROOT = /opt
PKG_NAME = $(COMPILER)-$(HOST)-$(MACHINE)

# Append release discriminant for repeat releases of the same compiler release.
PKG_FILE_NAME = $(PKG_NAME)

all: $(PWD)/product/$(PKG_FILE_NAME).pkg

$(PWD)/package/$(PKG_NAME).pkg:
	rm -rf package
	mkdir package
	pkgbuild						\
	  --root $(BUILD_ROOT)/$(COMPILER)-$(HOST)		\
	  --identifier $(PKG_NAME)			\
	  --version $(VERSION)					\
	  --install-location $(TARGET_ROOT)/$(COMPILER)-$(HOST)	\
	  --ownership recommended				\
	  $@

distribution.xml: distribution.xml-tmpl Makefile
	sed					\
	  -e "s|BUILD_NOTES|$(BUILD_NOTES)|g"	\
	  -e "s|COMPILER|$(COMPILER)|g"		\
	  -e "s|ARCH|$(ARCH)|g"			\
	  -e "s|HOST|$(HOST)|g"			\
	  -e "s|TARGET_ROOT|$(TARGET_ROOT)|g"	\
	  -e "s|MACHINE|$(MACHINE)|g"		\
	  -e "s|RELEASE|$(RELEASE)|g"		\
	  -e "s|VERSION|$(VERSION)|g"		\
	  <$<					\
	  >$@

resources/en.lproj/%.html: %.html-tmpl Makefile
	sed					\
	  -e "s|BUILD_NOTES|$(BUILD_NOTES)|g"	\
	  -e "s|COMPILER|$(COMPILER)|g"		\
	  -e "s|ARCH|$(ARCH)|g"			\
	  -e "s|HOST|$(HOST)|g"			\
	  -e "s|TARGET_ROOT|$(TARGET_ROOT)|g"	\
	  -e "s|MACHINE|$(MACHINE)|g"		\
	  -e "s|RELEASE|$(RELEASE)|g"		\
	  -e "s|VERSION|$(VERSION)|g"		\
	  <$<					\
	  >$@

html::
	-mkdir -p resources/en.lproj
html:: resources/en.lproj/welcome.html
html:: resources/en.lproj/license.html
html:: resources/en.lproj/readme.html
html:: resources/en.lproj/conclusion.html

$(PWD)/product/$(PKG_FILE_NAME).pkg: \
 $(PWD)/package/$(PKG_NAME).pkg distribution.xml html
	find . -name \*~ | xargs rm
	rm -rf product
	mkdir product
	productbuild						\
	  --distribution distribution.xml			\
	  --package-path $$PWD/package				\
	  --resources $$PWD/resources				\
	  $@

clean:
	rm -rf resources package product distribution.xml

.PHONY: all package product clean
