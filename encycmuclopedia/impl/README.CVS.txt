These files are based on the versions in the CVS-tree, and are
taken on the date mentioned in the startup message. The diff
between these files and the ones in the CVS-tree is in the
file diffs-with-cvs.

If you want to update the sources to a newer version, use:

export CVS_RSH=ssh  (or `setenv CVS_RSH ssh` in csh-derivates)
cvs -d anoncvs@anoncvs.cons.org:/home/CVS-cmucl update src/

You need ssh and cvs. Be prepared to edit out conflicts!

Update:

Recently the cvs server has been integrating more and more
my patches. I suspect that in the next few weeks all of my
diffs will be integrated in the repository. This means that
there will be conflicts between the source and the updates.
In most of the cases you should decide to follow the repository.
