
## CHANGELOG

* v0.9.x
    - /sbin/svdinit replaced original /sbin/init
    - Updated to HBSD branch: HardenedBSD-10-STABLE-v32.4
    - Lots of improvements to Governor
    - Switched to ZFS datasets managed under jails
    - Switched to ZFS origin of /Software
    - Exported Libucl to be available with C/C++ API

* v0.8.2
    - SecAdm imported to base os.
    - Exported Libucl to be available with C/C++ API

* v0.8.1
    - Build hosts (ng0x) now use HBSD base.
    - Fixed missing kernel security options.
    - Several changes to default configuration and devices.
    - Switched from Subversion to Git.
    - Switched whole project repository from vanilla FreeBSD 10.x to HardenedBSD 10-STABLE.

* v0.8.0
    - First attempts to build HBSD base system.

* v0.6.14
    - Base FreeBSD revision: 285875.

* v0.6.13
    - Fixed broken Dtrace support in kernel build.

* v0.6.12
    - Base FreeBSD revision: 285283.

* v0.6.11
    - Base FreeBSD revision: 284378.

* v0.6.10
    - Base FreeBSD revision: 284126.
    - Internal NFS server enabled.

* v0.6.9
    - Base FreeBSD revision: 282245.
    - Added WITH_BSD_JDK=TRUE to make.conf

* v0.6.8
    - Base FreeBSD revision: 280294.


* v0.6.7
    - Use Syslog-ng as system default.
    - Base FreeBSD revision: 279726.


* v0.6.6
    - Updated Git to version 2.2.2.
    - Added src.conf to build lldb with base system.
    - Install src.conf and make.conf with system.
    - Base FreeBSD revision: 277538.

* v0.6.4
    - Updated default Git bundle to version 2.2.x.
    - Merged jail-utils into one script. Introduced - Governor
    - A lots of improvements to worker environment
    - Automatically launch svdss on jail start
    - Replaced xz archive with minimal etc dir tree
    - Removed limits from login.conf

* v0.6.3
    - Removed bundled version of ServeD-OS in core
    - Update rc.conf only once on install / update action
    - Added support for RCTL resources limits
    - Install action will now automatically behave like update action when launched again on host with ServeD installed
    - Improved selecting default network interface for jail-create (default route)

* v0.6.2
    - Fixed version determining bugs
    - Fixed multiversioned jails support
    - More minor fixes

* v0.6.1
    - Automatic installation of Sofin for all subsystems.
    - Improved install - done handling retries
    - Added jail-utils to base system.
    - Updated etc-jail.tar.xz (added standard worker user, improved PAM contents, fixed login)

* v0.6.0
    - Updated Zsh to 5.0.7
    - Added support for 10.1
    - Split jails base for 10.1 and 9.3

* v0.4.10
    - Added Gcc to base
    - More minor updates

* v0.4.9
    - More specialized make.conf
    - Improved build-jail-base script.
    - Even less default dependencies in base

* v0.4.8
    - No more kernel in binbuild package.
    - Removed Patchelf from requirements.
    - Updated Zsh to version 5.0.6
    - Fixed issue with followed symlinks copied to exports/

* v0.4.7
    - Updated Sofin to v0.66.0
    - Support for both 9.3 and 10.0 hosts
    - Even less software bundled on ng0. Using Sofin on install part to fetch current base software

* v0.4.6
    - Installing kernel from
    - Removed Mysql from base system.
    - Added Bacula 7.0.5 build
    - Added /Jails to build. Versions: 9.3-p2, 10.0-p9
    - Updated sysctl.conf jail option
    - Several other improvements

* v0.4.5
    - updated Sofin to v0.64.4
    - Several tweaks to sysctl.conf.served due to svdss requirements
    - Several tweaks to loader.conf.served - increased shared memory limits
    - Multiple fixes

* v0.4.4
    - updated TheSS to v0.96.6
    - added automated installation from ServeD-OS repository (bundled with Base package)
    - /VERSION is now bundled with Base package
    - code cleanup, logger improvements

* v0.4.3
    - updated TheSS to v0.96.4
    - updated Sofin to v0.64.2
    - added Nginx and Most to base Software
    - fixed bad /SoftwareData path

* v0.4.2
    - updated rc configuration
    - clang built kernel with dtrace and rctl support
    - updated boot loader settings
    - updated TheSS to v0.96.3
    - updated Sofin to v0.64.1
    - added initial ZFS snapshots
