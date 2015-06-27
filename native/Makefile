SHELL=/bin/sh
# This makefile is used for installing the binary distribution of GNAT native.
# The installation script resulting from configuration consists
# of make commands using this makefile.  The options allowed by setting
# the directories by hand are greater than those allowed in the
# configuration script.

# Variables set up by doinstall
version       = 5.1.0
machine       = x86_64-apple-darwin13
built_prefix  = /opt/gcc-(version)
prefix        = $(built_prefix)

# Variables that don't need to be changed
gcc_lib       = gcc
libdir        = $(prefix)/lib
libsubdir     = $(prefix)/lib/$(gcc_lib)/$(machine)/$(version)
libexecsubdir = $(prefix)/libexec/$(gcc_lib)/$(machine)/$(version)
adaobjdir     = $(libsubdir)/adalib
adaincdir     = $(libsubdir)/adainclude
ext           =

ins-all: ins-basic ins-fixup

ins-basic:: mkdirs
	for f in $(prefix)/bin/$(machine)-gnat*	\
	    $(prefix)/bin/$(machine)-gcc; do	\
	    case $$f in				\
	        *$(machine)-gnatemu*) ;;	\
	        *) rm -f "$$f";;		\
	    esac;				\
	done

	for d in bin lib libexec include share $(machine); do	\
	    if [ -d $$d ]; then					\
	        tar cf - $$d | (cd $(prefix); tar xf -);	\
	    fi							\
	done

	-rm -rf $(libsubdir)/include-fixed
	-mkdir -p $(libsubdir)/include-fixed
	case $(machine) in						  \
	    *-lynxos*) ;;						  \
	    *) $(libexecsubdir)/install-tools/mkheaders -v -v $(prefix);; \
        esac

	case `uname` in						\
	    *_NT*)						\
	    for directory in $(libsubdir)/adalib		\
		$(libsubdir)/rts-*/adalib ; do			\
	        if [ -d $${directory} ]; then			\
	            (cd $${directory}; chmod a-w *.ali);	\
	        fi						\
	    done;;						\
	esac

# (Mac OS X) Fix up the dyld paths installed in executables and shared
# libraries to correspond to the actual installation prefix.

# The libraries, references to which we need to change. Note, we're in
# the distribution directory, so lib is an immediate subdirectory.
#
# '-depth 1' in case the compiler was built with multilib, and there's
# an i386/ subdirectory.
cpp   = $(shell find lib -depth 1 -type f -name libstdc++.\*.dylib)
gcc   = $(shell find lib -depth 1 -type f -name libgcc_s.\*.dylib)
gomp  = $(shell find lib -depth 1 -type f -name libgomp.\*.dylib)
qmth  = $(shell find lib -depth 1 -type f -name libquadmath.\*.dylib)

ins-fixup::
	@echo "fixing up executables in $(prefix)/bin"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			\
	    for ex in `find $(prefix)/bin -type f -perm -0111`; do	\
		if [ "`file $$ex | grep Mach-O`" ]; then		\
		    echo "fixing $$ex";					\
		    install_name_tool					\
			-change						\
			$(built_prefix)/$(cpp)				\
			$(prefix)/$(cpp)				\
			-change						\
			$(built_prefix)/$(gcc)				\
			$(prefix)/$(gcc)				\
			$$ex;						\
		fi;							\
	    done;							\
       fi

ins-fixup::
	@echo "fixing up executables in $(libexecsubdir)"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			\
	    for ex in `find $(libexecsubdir) -type f -perm -0111`; do	\
		if [ "`file $$ex | grep Mach-O`" ]; then		\
		    echo "fixing $$ex";					\
		    install_name_tool					\
			-change						\
			$(built_prefix)/$(cpp)				\
			$(prefix)/$(cpp)				\
			-change						\
			$(built_prefix)/$(gcc)				\
			$(prefix)/$(gcc)				\
			$$ex;						\
		fi;							\
	    done;							\
       fi

ins-fixup::
	@echo "fixing up shared library identification names"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			  \
	    for l in `find $(libdir) -depth 1 -name \*.dylib -type f`; do \
		if [ "`file $$l | grep 'shared library x86_64'`" ];	  \
		then							  \
		    echo "fixing $$l";					  \
		    install_name_tool -id $$l $$l;			  \
		fi;							  \
	    done;							  \
        fi

# This is for libgcc_ext.10.?.dylib. Must happen after identification
# names have been fixed.
#
# Couldn't find where this was done in the Makefiles; worked out by
# reading the log files.
ins-fixup::
	@echo "fixing up shared library stubs"
	if [ "$(prefix)" != "$(built_prefix)" ]; then	\
	    strip -curx					\
                -R libgcc-darwin.10.4.ver		\
		-o libgcc_ext.10.4.dylib_T		\
		$(libdir)/libgcc_s.1.dylib;		\
	    lipo -create				\
		-output $(libdir)/libgcc_ext.10.4.dylib	\
		libgcc_ext.10.4.dylib_T;		\
	    rm libgcc_ext.10.4.dylib_T;			\
	    strip -curx					\
		-R libgcc-darwin.10.5.ver		\
		-o libgcc_ext.10.5.dylib_T		\
		$(libdir)/libgcc_s.1.dylib;		\
	    lipo -create				\
		-output $(libdir)/libgcc_ext.10.5.dylib	\
		libgcc_ext.10.5.dylib_T;		\
	    rm libgcc_ext.10.5.dylib_T;			\
        fi

ins-fixup::
	@echo "fixing up shared library dependent names"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			\
	    for l in `find $(libdir) -name \*.dylib\* -type f`; do	\
		if [ "`file $$l | grep 'shared library x86_64'`" ];	\
		then							\
		    echo "fixing $$l";					\
		    install_name_tool					\
			-change						\
			$(built_prefix)/$(cpp)				\
			$(prefix)/$(cpp)				\
			-change						\
			$(built_prefix)/$(gcc)				\
			$(prefix)/$(gcc)				\
			-change						\
			$(built_prefix)/$(gomp)				\
			$(prefix)/$(gomp)				\
			-change						\
			$(built_prefix)/$(qmth)				\
			$(prefix)/$(qmth)				\
			$$l;						\
		fi;							\
	    done;							\
        fi

mkdirs:
	rm -fr $(libsubdir)/rts*
	rm -fr $(adaincdir) $(adaobjdir)
	mkdir -p $(prefix)
