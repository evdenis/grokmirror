GROK-MANIFEST
=============
---------------------------------------
Create manifest for use with grokmirror
---------------------------------------

:Author:    mricon@kernel.org
:Date:      2019-02-14
:Copyright: The Linux Foundation and contributors
:License:   GPLv3+
:Version:   1.2.0
:Manual section: 1

SYNOPSIS
--------
    grok-manifest [opts] -m manifest.js[.gz] -t /path [/path/to/bare.git]

DESCRIPTION
-----------
Call grok-manifest from a git post-update or post-receive hook to create
the latest repository manifest. This manifest file is downloaded by
mirror slaves (if newer than what they already have) and used to only
clone/pull the repositories that have changed since the mirror's last run.

OPTIONS
-------
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -m MANIFILE, --manifest=MANIFILE
                        Location of manifest.js or manifest.js.gz
  -t TOPLEVEL, --toplevel=TOPLEVEL
                        Top dir where all repositories reside
  -l LOGFILE, --logfile=LOGFILE
                        When specified, will put debug logs in this location
  -c, --check-export-ok
                        Honor the git-daemon-export-ok magic file and 
                        do not export repositories not marked as such
  -n, --use-now         Use current timestamp instead of parsing commits
  -p, --purge           Purge deleted git repositories from manifest
  -x, --remove          Remove repositories passed as arguments from
                        the manifest file
  -y, --pretty          Pretty-print the generated manifest (sort repos
                        and add indentation). This is much slower, so
                        should be used with caution on large
                        collections.
  -w, --wait-for-manifest
                        When running with arguments, wait if manifest is not
                        there (can be useful when multiple writers are writing
                        to the manifest file via NFS)
  -i IGNORE, --ignore-paths=IGNORE
                        When finding git dirs, ignore these paths (can be used
                        multiple times, accepts shell-style globbing)
  -v, --verbose         Be verbose and tell us what you are doing

EXAMPLES
--------
The examples assume that the repositories are located in /repos. If your
repositories are in ``/var/lib/git``, adjust both ``-m`` and ``-t``
flags accordingly.

Initial manifest generation::

    /usr/bin/grok-manifest -m /repos/manifest.js.gz -t /repos

Inside the git hook::

    /usr/bin/grok-manifest -m /repos/manifest.js.gz -t /repos -n `pwd`

To purge deleted repositories, use the ``-p`` flag when running from
cron::

    /usr/bin/grok-manifest -m /repos/manifest.js.gz -t /repos -p

You can also add it to the gitolite's D command using the ``-x`` flag::

    /usr/bin/grok-manifest -m /repos/manifest.js.gz -t /repos -x $repo.git

To troubleshoot potential problems, you can pass ``-l`` parameter to
grok-manifest, just make sure the user executing the hook command (user
git or gitolite, for example) is able to write to that location::

    /usr/bin/grok-manifest -m /repos/manifest.js.gz -t /repos \
        -l /var/log/git/grok-manifest-hook.log -n `pwd`

SEE ALSO
--------
* grok-pull(1)
* git(1)

SUPPORT
-------
Please open an issue on Github:

    https://github.com/mricon/grokmirror/issues
