# mrbStudio - Marios' rsync backup studio #
_A script for versioned backups of both local and remote filesystems._

Copyright 2009-2013 Marios Andreopoulos ( opensource at andmarios dot com )

Distributed under the terms of the GNU General Public License v2
___


This is a script that automates the task of taking versioned backups of your filesystems. I know there are so many such scripts out there that I could just have named it YABS (yet another backup script) but this name is probably already taken.

I decided to make it public, because it really helped me with my backups policy. It isn't an original idea, rather an implentation of guides I read in the past that turned out to work rather well.


So, what it really does, is to copy the folder you want to backup to the backup directory (while preserving ownerships, attributes, special files, links, etc). This happens for the first backup though. For the next backups, it creates a hard link copy or a snapshot (if you are using btrfs) of the most recent backup and copies only the changed files. This makes the process very quick and the space needed for a new version relatively small. On the
same time, your backups are directories, you can browse them, copy them, mount them or do anything you like without special software.


This is the thing I need most from a backup: a backup that works and doesn't need special tools or maintenance. Any recovery function, whether it be for a single file or the whole filesystem, should be able to be performed as quick as possible by anyone with a shell (your system, a live cd, anything) and no special tools. mrbStudio only creates backups, you don't need it for accessing or restoring them. It does not do that.


## Features:
 - Versioned backups of local and remote filesystems (through ssh) to a local filesystem.
 - Easy to run once you create your configuration files, no need to postpone your backups again.
 - Hard links or btrfs snapshots are used for backup versions in order to save time and space.
 - Easy way to exclude files from backups.
 - Automatic mounting of local and/or remote filesystems if needed.
 - Backups are exact copies of the original directories, no special software needed to access them, only a shell.
 - Script is compact and easy to modify. You need an extra feature? You can implement it fast.

 
How it works? You create a configuration file for each filesystem you want to backup. Check the _sample.mrbStudio_ and the examples to get the idea; it is easy.
Then you just call it with the configuration file:

`` $ mrbStudio sample.mrbStudio ``


mrbStudio does not support removing old backups. You have to do this manually.


A last note; mrbStudio isn't rocket science and certainly isn't an advanced bash script. One should not play with his backups, they are there to save you from a catastrophe. So, I intentionally kept it basic. What it really does is to make a bunch of checks to see if your configuration file is sane (you will probably run it as root, so you don't want things to go wrong) and then calls rsync. Essentially it is a wrapper for rsync.


## mrbStudio-helper

This helper script goes to the remote machine and helps you easily create/remove LVM snapshots, lock/unlock MySQL tables, etc.
Essentially it is a wrapper for mount, umount, lvcreate, lvremove, mysql.
For example, instead of your typical remote mount command, you could run:

`` $ mrbStudio-helper -m "$(cat /path/to/passfile)" -p /dev/vg-1/partition -l snapshot -t /mnt/backup -s``

The above command would lock MySQL's tables, then create a 1G snapshot of `/dev/vg-1/partition` at `/dev/vg-1/snapshot-randomstring`,
then unlock MySQL's tables and finally mount `/dev/vg-1/snapshot-randomstring` at `/mnt/backup`.

To umount, you would then run:

`` $ mrbStudio-helper -p /dev/vg-1/partition -l snapshot -t /mnt/backup -k``

___


## Changelog ##

### v4.10 - 2013, Jan 02 ([browse](http://github.com/andmarios/mrbStudio/tree/v4.10), [zip](http://github.com/andmarios/mrbStudio/zipball/v4.10), [tar](http://github.com/andmarios/mrbStudio/tarball/v4.10))
mrbStudio-helper specific:

   - fixed bug of MySQL not staying locked during LVM snapshotting
   - dropped support for locking MySQL on non LVM-snapshot based backups (never worked as it turned out)

### v4.00 - 2012, Nov 29 ([browse](http://github.com/andmarios/mrbStudio/tree/v4.00), [zip](http://github.com/andmarios/mrbStudio/zipball/v4.00), [tar](http://github.com/andmarios/mrbStudio/tarball/v4.00))
   - bugfix: fixed bug where when no excludes, rsync would go rogue
   - added mrbStudio-helper, main reason for such great bump
   - removed some redirections of stdout and/or stderr to /dev/null, in order to make it easier to catch the cause of errors

mrbStudio-helper specific:

   - mount/umount remote block device
   - create/remove LVM snapshot for remote block device
   - lock/unlock MySQL on remote system (unlock immediately if on LVM, after sync if not)

### v3.01 - 2012, Jun 14 ([browse](http://github.com/andmarios/mrbStudio/tree/v3.01), [zip](http://github.com/andmarios/mrbStudio/zipball/v3.01), [tar](http://github.com/andmarios/mrbStudio/tarball/v3.01))
   - bugfix: mrbStudio didn't create a btrfs subvolume on the first run for a btrfs backup configuration

### v3.0  - 2012, Jun 13 ([browse](http://github.com/andmarios/mrbStudio/tree/v3.0), [zip](http://github.com/andmarios/mrbStudio/zipball/v3.0), [tar](http://github.com/andmarios/mrbStudio/tarball/v3.0))
   - added btrfs snapshot support
   - replaced cp -l with rsync --link-dest option (much faster for large filesystems)
   - code restructure and clean up in order to release in public
   - excluded files' list may be inside a main configuration file instead of a seperate file
   - better documentation
   - caution: this version changed the names of some variables, so older configuration files may need changes

### v2.0  - 2012, Jan 12 ([browse](http://github.com/andmarios/mrbStudio/tree/v2.0), [zip](http://github.com/andmarios/mrbStudio/zipball/v2.0), [tar](http://github.com/andmarios/mrbStudio/tarball/v2.0))
   - added versioned backup for remote linux systems through ssh and rsync

### v1.0  - 2009, Jun 30 ([browse](http://github.com/andmarios/mrbStudio/tree/v1.0), [zip](http://github.com/andmarios/mrbStudio/zipball/v1.0), [tar](http://github.com/andmarios/mrbStudio/tarball/v1.0))
   - versioned backup of local fileystems
