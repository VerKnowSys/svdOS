
## CHANGELOG

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
