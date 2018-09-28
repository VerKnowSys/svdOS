svdOS
=========

"Because you deserve to be ServeD!"


## Manual installation:

0. Install 64bit [HardenedBSD](https://hardenedbsd.org/) or [FreeBSD](https://www.freebsd.org/) with version: `11.0+`. If you wish to install svdOS on local vm created with ZFS and xHyve, you may like to [follow steps from README here](https://github.com/VerKnowSys/xhyve).

1. `fetch -o - https://raw.githubusercontent.com/VerKnowSys/svdOS/master/svdup | sh` to install ServeD release/update.


## Dedicated machine preparation

> NOTE: I use: [EX-LINE](https://www.hetzner.com/dedicated-rootserver/matrix-ex) server type, (Ryzen doesn't work with FreeBSD 11-STABLE)

0. Login to: [Hetzner Robot](https://robot.your-server.de/),

1. Activate rescue system with version `FreeBSD 11.1`. Reboot system from web panel: `CB2->Reset->Execute an automatic hardware reset`. Confirm, wait ~48s (default boot time 30s + ~18s for kernel).

2. Login to rescue system via SSH using provided root password: `ssh root@YOUR-SRV-IP`.

3. Run `bsdinstallimage` (script provided by Hetzner). Follow steps:
    - Pick FreeBSD 11.2, 64bit,
    - Continue with default keymap,
    - Enter desired "myhostname",
    - Unselect all system components on list,
    - Pick "Auto (ZFS)" option:
        - Pool disk - use "mirror", with both disks,
        - Use defaults for other ZFS settings,
        - Hit "Install", confirm that we wish to create new pool,
    - Installation shouldn't take more than a minute, after that type new root password,
    - Select default network card (em0), use DHCP over IPv4, disable IPv6,
    - Wipe out value of the first "Search" field,
    - Select "NO" when asked about UTC, pick "Europe", "Sweden", confirm it's "CEST",
    - Skip date/time set up,
    - Pick: "sshd", "ntpd", and "dumpdev" system services,
    - Select all system-hardening options but "Disable process debugging facilities for unprivileged users",
    - Don't add any system users,
    - Hit Enter on "Exit" from menu, confirm that you wish to open shell to make additional modification of the system,
    - Run `sed -i '' -e 's|#PermitRootLogin no|PermitRootLogin yes|; s|#Port 22|Port 60022|' /etc/ssh/sshd_config` in console, and hit Ctrl-d (or type `exit`).

4. Reboot to the new vanilla `FreeBSD-11.2` system.

5. On your local workstation - clone [Shable](https://github.com/VerKnowSys/Shable): `git clone https://github.com/VerKnowSys/Shable`,

6. Do: `cd Shable && echo "myhostname ip=11.22.33.44 remote_vpn_server=22.33.44.55 default_jails_domain='mydomain.com'" >> inventory`. You can also override default internal network which is: `172.16.3` to own one, by passing additional argument: `vpn_network=172.16.123`.

7. Generate new passwordless OpenVPN key and certificate for your new dedicated server. Put `ca.crt` (from "your OpenVPN server") + `your-inventory-host-name.crt` + `your-inventory-host-name.key` files under `templates/vpn/` directory.

8. Do `./setup-dedicated-system myhostname` to complete system installation.


## Built in features:

* Hardened kernel and base system (prebuilt with support for all [HardenedBSD features](https://hardenedbsd.org/content/easy-feature-comparison)).

* Support for full network stack under jails (VIMAGE).

* Root filesystem (`/`) mounted read-only by default (overrideable).

* Software datasets read-only by default (overrideable).

* Limited superuser access to base system only. Listening to ports <1024 is allowed for jail default user (`worker`).

* Preconfigured ZSH shell with cherry picked modules from [Oh My ZSH](https://github.com/robbyrussell/oh-my-zsh).

* [Sofin](https://github.com/VerKnowSys/sofin) hardened software is default, but you can still use `pkg` utility or you can even use ports (not supported!).

* Beadm driven ZFS boot environments (with binary upgrade support, and easy system rollbacks).

* ZFS driven Jail Governor - [gvr](https://github.com/VerKnowSys/svdOS/blob/master/gvr) - installed by default when setting up dedicated system.

* ZFS driven software and configuration management (very fast, block level software installation - thx to Sofin).

* DTrace hooks built in (when supported by software) - provided by Sofin. Note, that there's an issue related to this feature at the moment: https://github.com/HardenedBSD/hardenedBSD/issues/305.



## My other projects that are part of the system:

* [Shable](https://github.com/VerKnowSys/Shable) - "Ansible" compliant software written in pure `sh`.

* [Sofin](https://github.com/VerKnowSys/sofin) - "Software Installer" also written in pure `sh`.

* [Sofin-definitions](https://github.com/VerKnowSys/sofin-definitions/) - Sofin software definitions - all pure `sh`.

* [svdOS](https://github.com/VerKnowSys/svdOS) - this repo, with: [system build script](https://github.com/VerKnowSys/svdOS/blob/master/build-all), [production kernel configuration](https://github.com/VerKnowSys/svdOS/blob/master/kernel/VerKnowSys-11), other [configuration files](https://github.com/VerKnowSys/svdOS/tree/master/etc) and [system installer and updater script](https://github.com/VerKnowSys/svdOS/blob/master/svdup).


## But… why?

* I've learned a lot about systems, software hardening and security.

* It's a challenge and I this is my PoC of how to provide modern, concise and clean server system.

* I wish to do a server system, which will get as close in quality as macOS is for workstation systems.

* It took me 7 years to implement all the stuff, so I guess it's a lot of fun too ;)


## License

BSD/MIT/IDGAF


