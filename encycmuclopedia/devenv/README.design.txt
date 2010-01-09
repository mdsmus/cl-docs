		Design of the Common Lisp controller

	Introduction

At present there is no clear cut way for a Common Lisp implementation
to find a installed Common Lisp library. Ad-hoc solutions are just not
good enough:

	- user's want to be able to see the source code of the library
	- libraries sometimes need to be recompiled when an
	  incompatible version of the implementation has been used to
	  compile the current fasl files.
	- implementations and libraries need a common protocol to find
	  each other.

In this document I propose a solution to these problems, these idea's
will form the basis of a Debian package Common-Lisp-Controller that
will implement the needed infrastructure. Each Common Lisp
Implementation and Library package for Debian should then follow a few
simple guidelines so 
(require 'nirvana)
should 'just work'.

	Basic Roadmap

We use logical pathnames to point implementations to the
libraries. The setting up of the logical pathnames is the
responsibility of the implementation.

The libraries and system files are installed in pre-defined locations
so they can be found automagicly. 

The main idea is the following:

Put the source trees under:
/usr/share/common-lisp/source/<package-name>

This can, and in  most cases will be a symbolic link to
a directory in
/usr/share/common-lisp/repositories/...

Here there is a place to put for example sourceforge-hosted
libraries. These can then be automaticly updated with cvs
commands. Note too that <package-name> has to be a portable pathname,
so can only contain A-Z0-9- (a-z also seems to work most of the
time). Placing the true files in the repository directory and making
only a confirming symbolic link can help in getting a valid logical
pathname (for example we link /usr/share/common-lisp/source/defsystem
to
/usr/share/common-lisp/repositories/sourceforge/clocc/clocc/src/defsystem-3.x).

The system definitions in
/usr/share/common-lisp/systems/<package-name>.system
In those system files you should reference your sourcecode via the
"cl-library:;<package-name>;foo.lisp" logical pathname.
FIXME: for sbcl only "cl-library:<package-name>;foo.lisp" works!

The MK:DEFSYSTEM will then compile that file to 
/usr/lib/common-lisp/<name>/<package-name>/foo.<fasl of that lisp> 
As this is done with the logical pathname stuff the implementation
should worry about the <name> and <fasl of that lisp> part.

	Common-Lisp-Controller
	
This is the basic setup. The remainder is more part of
Common-Lisp-Controller and the general care and maintenance of a
system. After installation a Common Lisp Implementation should put a
script in 
/usr/lib/common-lisp/bin/<name>.sh
with name the name of the implementation. Notice the /usr/lib. This is
because we want to be able to share /usr/share between different
machines. As a Common Lisp Implementation is pretty bound to a certain
machine I think that file should go there.

Every common lisp code package should copy the files in the source and
systems directories. Then register with:

register-common-lisp-source <package-name>

Every common lisp implementation should install help scripts and
binaries in the bin directory and then register with: 

register-common-lisp-implementation <name>

The name should be the name of the implementation, eg. "cmucl-safe".

The script should be a shell script or binary accepting the following
commands:

	<name>.sh rebuild <package-name>+
The should load and recompile the given packages. It should
load and use mk-defsystem automagicly. It should also if possible
create the libraries for that implementation.

	<name>.sh remove <package-name>*
This should remove all fasl, log and other files belonging
to the package

Notice that there are subtle security problems here: we don't want
something as powerful as a common lisp compiler running as root, but
we need to have root as the owner of the fasl's for obvious
reasons. The solution is to re-chown the /usr/lib/common-lisp hierarchy
to the user "nobody", compile the stuff as user "nobody" and then
re-chown the hierarchy back to root. In this was we run the compiler
as a user who can do little harm, and at the same time the resulting
files will be safe. Notice that we should change the umask to 077 to
avoid generating writable files.

To remove a common lisp code package:
unregister-common-lisp-source <package-name>
this commands also removes all compiled files and library files.

To remove a common lisp compiler:
unregister-common-lisp-implementation <name> <sub-name>
All relevant fasl's will be removed.
