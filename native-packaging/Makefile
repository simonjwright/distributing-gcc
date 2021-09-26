BUILD_ROOT = /opt
COMPILER = gcc-$(RELEASE)
RELEASE = 11.2.0
VERSION = 1
MACHINE = x86_64-apple-darwin15
TARGET_ROOT = /opt

all: $(PWD)/product/$(COMPILER)-$(MACHINE).pkg

$(PWD)/package/$(COMPILER)-$(MACHINE).pkg:
	rm -rf package
	mkdir package
	pkgbuild						\
	  --root $(BUILD_ROOT)/$(COMPILER)			\
	  --identifier $(COMPILER)-x86_64-apple-darwin15	\
	  --version $(VERSION)					\
	  --install-location $(TARGET_ROOT)/$(COMPILER)		\
	  --ownership recommended				\
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

clean:
	rm -rf package product

.PHONY: all package product clean
