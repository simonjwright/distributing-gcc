SHELL=/bin/sh
# This makefile is used for installing the binary distribution of GNAT cross.
# The installation script resulting from configuration consists
# of make commands using this makefile.  The options allowed by setting
# the directories by hand are greater than those allowed in the
# configuration script.

# We need to know where the builder compiler was, so that we can
# adjust the dyld paths for the installed location if different.
builder_prefix  = /usr/local/gnat

# This is where the compiler is to be installed
prefix        = $(builder_prefix)

# We need to know how to construct subdirectory names
version       = 1.2.3
machine       = arm-eabi

#####################################################################
# No need to change anything below

gcc_lib       = gcc
libsubdir     = $(prefix)/lib/$(gcc_lib)/$(machine)/$(version)
libexecsubdir = $(prefix)/libexec/$(gcc_lib)/$(machine)/$(version)
adaobjdir     = $(libsubdir)/adalib
adaincdir     = $(libsubdir)/adainclude
ext           =

ins-all: ins-basic ins-fixup

ins-basic:: mkdirs
	for f in $(prefix)/bin/$(machine)-gnat* $(prefix)/bin/$(machine)-gcc; do \
	   case $$f in \
	      *$(machine)-gnatemu*) ;; \
	                        *) rm -f "$$f";; \
	   esac; \
	done

	for d in bin lib libexec include share $(machine); do \
	   if [ -d $$d ]; then \
	      tar cf - $$d | (cd $(prefix); tar xf -); \
	   fi \
	done

	-rm -rf $(libsubdir)/include-fixed
	-mkdir -p $(libsubdir)/include-fixed
	case $(machine) in \
	   *-lynxos*) ;; \
	   *) $(libexecsubdir)/install-tools/mkheaders -v -v $(prefix);; \
        esac

	case `uname` in \
	*_NT*) \
	for directory in $(libsubdir)/adalib $(libsubdir)/rts-*/adalib ; do \
	  if [ -d $${directory} ]; then \
	    (cd $${directory}; chmod a-w *.ali); \
	  fi \
	done;; \
	esac

# (Mac OS X) Fix up the dyld paths installed in executables and shared
# libraries to correspond to the actual installation prefix.

# The libraries, references to which we need to change. Note, we're in
# the distribution directory, so lib is an immediate subdirectory.
#
# '-depth 1' in case the compiler was built with multilib, and there's
# an i386/ subdirectory.
cpp = $(shell find lib -depth 1 -type f -name libstdc++\*.dylib)
gcc = $(shell find lib -depth 1 -type f -name libgcc_s\*.dylib)

ins-fixup::
	@echo "fixing up executables in $(prefix)/bin"
	if [ "$(prefix)" != "$(builder_prefix)" ]; then			    \
	    for ex in `find $(prefix)/bin -name $(machine)-\* -perm -0111`; \
	    do								    \
		if [ "`file $$ex | grep Mach-O`" ]; then		    \
		    echo "fixing $$ex";					    \
		    install_name_tool					    \
			-change						    \
			$(builder_prefix)/$(cpp)			    \
			$(prefix)/$(cpp)				    \
			-change						    \
			$(builder_prefix)/$(gcc)			    \
			$(prefix)/$(gcc)				    \
			$$ex;						    \
		fi;							    \
	    done;							    \
	fi

ins-fixup::
	@echo "fixing up executables in $(libexecsubdir)"
	if [ "$(prefix)" != "$(built_prefix)" ]; then			\
	    for ex in `find $(libexecsubdir) -type f -perm -0111`; do	\
	       if [ "`file $$ex | grep Mach-O`" ]; then			\
		   echo "fixing $$ex";					\
		   install_name_tool					\
		       -change						\
		       $(builder_prefix)/$(cpp)				\
		       $(prefix)/$(cpp)					\
		       -change						\
		       $(builder_prefix)/$(gcc)				\
		       $(prefix)/$(gcc)					\
		       $$ex;						\
	       fi;							\
	    done;							\
        fi


mkdirs:
	rm -fr $(libsubdir)/rts*
	rm -fr $(adaincdir) $(adaobjdir)
	mkdir -p $(prefix)
