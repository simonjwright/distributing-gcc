BUILD_ROOT = /opt
COMPILER = gnat-ce-$(RELEASE)
RELEASE=2021
GCC_VERSION = 10.3.1
MACHINE = x86_64-apple-darwin15
TARGET_ROOT = /opt

all: $(PWD)/product/$(COMPILER)-$(MACHINE).pkg

$(PWD)/package/$(COMPILER)-$(MACHINE).pkg:
	rm -rf package
	mkdir package
	pkgbuild							\
	  --root $(BUILD_ROOT)/$(COMPILER)				\
	  --identifier gnat-ce-x86_64-apple-darwin15			\
	  --version $(RELEASE)						\
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

clean:
	rm -rf package product

.PHONY: all package product clean
