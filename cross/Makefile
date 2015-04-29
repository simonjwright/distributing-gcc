SHELL=/bin/sh
# This makefile is used for installing the binary distribution of GNAT cross.
# The installation script resulting from configuration consists
# of make commands using this makefile.  The options allowed by setting
# the directories by hand are greater than those allowed in the
# configuration script.

version       = 5.1.0
machine       = arm-eabi

# We need to know where the builder compiler was, so that we can
# adjust the dyld paths for the installed location if different.
#
# This is SJW's preferred style.
builder_prefix  = /opt/gcc-$(version)

prefix        = $(builder_prefix)
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

# (Mac OS X) Fix up the dyld paths installed in executables to
# correspond to the actual installation prefix.
ins-fixup::
	if [ "$(prefix)" != "$(builder_prefix)" ]; then			   \
	   cpp=`cd $(prefix); find lib -type f -name libstdc++\*.dylib`;  \
	   gcc=`cd $(prefix); find lib -type f -name libgcc_s\*.dylib`;   \
	   for ex in `find $(prefix)/bin -name $(machine)-\* -perm -0111`; \
	   do								   \
	       if [ "`file $$ex | grep Mach-O`" ]; then			   \
		   install_name_tool					   \
			-change						   \
			$(builder_prefix)/$$cpp				   \
			$(prefix)/$$cpp					   \
			-change						   \
			$(builder_prefix)/$$gcc				   \
			$(prefix)/$$gcc					   \
			$$ex;						   \
	       fi;							   \
	   done;							   \
	   for ex in `find $(libexecsubdir) -type f -perm -0111`; do	   \
	       if [ "`file $$ex | grep Mach-O`" ]; then			   \
		   install_name_tool					   \
			-change						   \
			$(builder_prefix)/$$cpp				   \
			$(prefix)/$$cpp					   \
		   	-change						   \
			$(builder_prefix)/$$gcc				   \
			$(prefix)/$$gcc					   \
			$$ex;						   \
	       fi;							   \
	   done;							   \
       fi


mkdirs:
	rm -fr $(libsubdir)/rts*
	rm -fr $(adaincdir) $(adaobjdir)
	mkdir -p $(prefix)
