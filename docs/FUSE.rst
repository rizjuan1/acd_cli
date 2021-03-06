FUSE module
===========

Status
------

The FUSE support is still in its early stage and may be
(`prone to bugs <https://github.com/yadayada/acd_cli/labels/FUSE>`_).
acd\_cli's FUSE module has the following filesystem features implemented:

=====================  ===========
Feature                 Working
=====================  ===========
Basic operations
----------------------------------
List directory           ✓
Read                     ✓
Write                    ✓ [#]_
Rename                   ✓
Move                     ✓
Trashing                 ✓
OS-level trashing        ✓ [#]_
View trash               ❌
Misc
----------------------------------
Automatic sync           ✓
ctime/mtime update       ❌
Custom permissions       ❌
Hard links               partially [#]_
Symbolic links           ❌ [#]_
=====================  ===========

.. [#] partial writes are not possible (i.e. writes at random offsets)
.. [#] restoring might not work
.. [#] manually created hard links will be displayed, but it is discouraged to use them
.. [#] soft links are not part of the ACD API

Usage
-----

The command to mount the (root of the) cloud drive to the empty directory ``path/to/mountpoint`` is
::

    acd_cli mount path/to/mountpoint

A cloud drive folder may be mounted similarly, by
::

    acd_cli mount --modules="subdir=/folder" path/to/mountpoint

Unmounting is usually achieved by the following command
::

    fusermount -u path/to/mountpoint

If the mount is busy, the ``--lazy`` (``-z``) flag can be used.
There exists a convenience action ``acd_cli umount`` that unmounts all ACDFuse mounts.

Mount options
~~~~~~~~~~~~~

For further information on the most of the options below, see your mount.fuse man page.

To convert the node's standard character set (UTF-8) to the system locale, the modules argument
may be used, e.g. ``--modules="iconv,to_code=CHARSET"``.

--allow-other, -ao        allow all users to access the mountpoint (may need extra configuration)
--allow-root, -ar         allow the root user to access the mountpoint (may need extra configuration)
--foreground, -fg         do not detach process until filesystem is destroyed (blocks)
--interval INT, -i INT    set the node cache sync (refresh) interval to INT seconds
--nlinks, -n              calculate the number of links for folders (slower)
--nonempty, -ne           allow mounting to a non-empty mount point
--read-only, -ro          disallow write operations (does not affect cache refresh)
--single-threaded, -st    disallow multi-threaded FUSE operations

Library Path
~~~~~~~~~~~~

If you want or need to override the standard libfuse path, you may set the environment variable
`LIBFUSE_PATH` to the full path of libfuse, e.g.
::

   export LIBFUSE_PATH="/lib/x86_64-linux-gnu/libfuse.so.2"

This is particularly helpful if the libfuse library is properly installed, but not found.

Logging
~~~~~~~

For debugging purposes, the recommended command to run is
::

    acd_cli -d mount -i0 -fg path/to/mountpoint

That command will disable the automatic refresh (i.e. sync) of the node cache (`-i0`) and disable
detaching from the console.
