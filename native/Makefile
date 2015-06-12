SHELL=/bin/sh
# This makefile is used for installing the binary distribution of GNAT native.
# The installation script resulting from configuration consists
# of make commands using this makefile.  The options allowed by setting
# the directories by hand are greater than those allowed in the
# configuration script.

version       = 5.1.0
machine       = x86_64-apple-darwin13
built_prefix  = /usr/local/gnat/$(version)
prefix        = $(built_prefix)
gcc_lib       = gcc
libdir        = $(prefix)/lib
libsubdir     = $(prefix)/lib/$(gcc_lib)/$(machine)/$(version)
libexecsubdir = $(prefix)/libexec/$(gcc_lib)/$(machine)/$(version)
adaobjdir     = $(libsubdir)/adalib
adaincdir     = $(libsubdir)/adainclude
ext           =

ins-all: ins-basic ins-fixup

ins-basic:: mkdirs
	for f in $(prefix)/bin/$(machine)-gnat* $(prefix)/bin/$(machine)-gcc; \
	do								      \
	   case $$f in							      \
	      *$(machine)-gnatemu*) ;;					      \
	                        *) rm -f "$$f";;			      \
	   esac;							      \
	done

	for d in bin lib libexec include share $(machine); do	\
	   if [ -d $$d ]; then					\
	      tar cf - $$d | (cd $(prefix); tar xf -);		\
	   fi							\
	done

	-rm -rf $(libsubdir)/include-fixed
	-mkdir -p $(libsubdir)/include-fixed
	case $(machine) in						 \
	   *-lynxos*) ;;						 \
	   *) $(libexecsubdir)/install-tools/mkheaders -v -v $(prefix);; \
        esac

	case `uname` in							    \
	*_NT*)								    \
	for directory in $(libsubdir)/adalib $(libsubdir)/rts-*/adalib ; do \
	  if [ -d $${directory} ]; then					    \
	    (cd $${directory}; chmod a-w *.ali);			    \
	  fi								    \
	done;;								    \
	esac

# (Mac OS X) Fix up the dyld paths installed in executables and shared
# libraries to correspond to the actual installation prefix.
ins-fixup::					\
  ins-fixup-executables-in-bin			\
  ins-fixup-executables-in-libexecsubdir	\
  ins-fixup-shared-library-identification-names	\
  ins-fixup-shared-library-stubs		\
  ins-fixup-shared-library-dependent-names

ins-fixup-executables-in-bin:
	@echo "fixing up executables in $(prefix)/bin"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			\
	    cpp=`find lib -type f -name libstdc++\*.dylib`;		\
	    gcc=`find lib -type f -name libgcc_s\*.dylib`;		\
	    for ex in `find $(prefix)/bin -type f -perm -0111`; do	\
		if [ "`file $$ex | grep Mach-O`" ]; then		\
		    echo "fixing $$ex";					\
		    install_name_tool					\
			-change						\
			$(built_prefix)/$$cpp				\
			@rpath/$$cpp					\
			-change						\
			$(built_prefix)/$$gcc				\
			@rpath/$$gcc					\
			$$ex;						\
		fi;							\
	    done;							\
       fi

ins-fixup-executables-in-libexecsubdir:
	@echo "fixing up executables in $(libexecsubdir)"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			\
	    cpp=`find lib -type f -name libstdc++\*.dylib`;		\
	    gcc=`find lib -type f -name libgcc_s\*.dylib`;		\
	    for ex in `find $(libexecsubdir) -type f -perm -0111`; do	\
		if [ "`file $$ex | grep Mach-O`" ]; then		\
		    echo "fixing $$ex";					\
		    install_name_tool					\
			-change						\
			$(built_prefix)/$$cpp				\
			@rpath/$$cpp					\
			-change						\
			$(built_prefix)/$$gcc				\
			@rpath/$$gcc					\
			$$ex;						\
		fi;							\
	    done;							\
       fi

ins-fixup-shared-library-identification-names:
	@echo "fixing up shared library identification names"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			  \
	    for l in `find $(libdir) -depth 1 -name \*.dylib -type f`; do \
		if [ "`file $$l | grep 'shared library x86_64'`" ];	  \
		then							  \
		    echo "fixing $$l";					  \
		    install_name_tool -id @rpath/`basename $$l` $$l;	  \
		fi;							  \
	    done;							  \
        fi

# This is for libgcc_ext.10.?.dylib. Must happen after identification
# names have been fixed.
#
# Couldn't find where this was done in the Makefiles; worked out by
# reading the log files.
ins-fixup-shared-library-stubs:
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

ins-fixup-shared-library-dependent-names:
	@echo "fixing up shared library dependent names"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			\
	    cpp=`find lib -type f -name libstdc++\*.dylib`;		\
	    gcc=`find lib -type f -name libgcc_s\*.dylib`;		\
	    for l in `find $(libdir) -name \*.dylib\* -type f`; do	\
		if [ "`file $$l | grep 'shared library x86_64'`" ];	\
		then							\
		    echo "fixing $$l";					\
		    install_name_tool					\
			 -change					\
			 $(built_prefix)/$$cpp				\
			 @rpath/$$cpp					\
			 -change					\
			 $(built_prefix)/$$gcc				\
			 @rpath/$$gcc					\
			 $$l;						\
		fi;							\
	    done;							\
        fi

mkdirs:
	rm -fr $(libsubdir)/rts*
	rm -fr $(adaincdir) $(adaobjdir)
	mkdir -p $(prefix)
