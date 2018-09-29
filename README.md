svdOS
=========

"Because you deserve to be ServeD!"


## Authors:

* Daniel ([dmilith](https://twitter.com/dmilith)) Dettlaff (2011-…)


## Manual installation:

0. Install 64bit [HardenedBSD](https://hardenedbsd.org/) or [FreeBSD](https://www.freebsd.org/) with version: `11.0+`. If you wish to install svdOS on local vm created with [ZFS](http://www.open-zfs.org/wiki/Main_Page) and [xHyve](https://github.com/mist64/xhyve), you may like to [follow steps from README here](https://github.com/VerKnowSys/xhyve).

1. `fetch -o - https://raw.githubusercontent.com/VerKnowSys/svdOS/master/svdup | sh` to install ServeD release/update.


## Dedicated machine preparation

> NOTE: I use: [EX-LINE](https://www.hetzner.com/dedicated-rootserver/matrix-ex) server type, (Ryzen doesn't work with FreeBSD 11-STABLE).

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

5. On your local workstation - clone [Shable](https://github.com/VerKnowSys/Shable): `git clone https://github.com/VerKnowSys/Shable`.

6. Do: `cd Shable && echo "myhostname ip=11.22.33.44 remote_vpn_server=22.33.44.55 default_jails_domain='mydomain.com'" >> inventory`. You can also override default internal network which is: `172.16.3` to own one, by passing additional argument: `vpn_network=172.16.123`.

7. Generate new passwordless OpenVPN key and certificate for your new dedicated server. Put `ca.crt` (from "your OpenVPN server") + `your-inventory-host-name.crt` + `your-inventory-host-name.key` files under `templates/vpn/` directory.

8. Do `./setup-dedicated-system myhostname` to complete system installation.


## Built in features:

* ServeD system implies, that a single dedicated machine is part of a "distributted infrastructure". By default, [OpenVPN](https://openvpn.net/) is used to provide internal network access for all jails automatically. NOTE: It's necessary to provide your private VPN keys. Without them installation will not be possible at the moment (overridable - maybe even supported one day).

* Hardened kernel and base system (prebuilt with support for all [HardenedBSD features](https://hardenedbsd.org/content/easy-feature-comparison)).

* Support for full network stack under jails ([VIMAGE](https://wiki.freebsd.org/VIMAGE)).

* Preconfigured [PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html) to [NAT](https://en.wikipedia.org/wiki/Network_address_translation) external network for jails.

* Root filesystem (`/`) mounted read-only by default (overrideable).

* Software datasets read-only by default (overrideable).

* Limited superuser access to base system only. Listening to ports <1024 is allowed for jail default user (`worker`).

* Preconfigured ZSH shell with cherry picked modules from [Oh My ZSH](https://github.com/robbyrussell/oh-my-zsh).

* [Sofin](https://github.com/VerKnowSys/sofin) hardened software is default, but you can still use `pkg` utility or you can even use ports (not supported!).

* Beadm driven ZFS boot environments (with binary upgrade support, and easy system rollbacks).

* ZFS driven Jail Governor - [gvr](https://github.com/VerKnowSys/svdOS/blob/master/gvr) - installed by default when setting up dedicated system.

* ZFS driven software and configuration management (very fast, block level software installation - thx to Sofin).

* [DTrace](https://github.com/opendtrace) hooks built in (when supported by software) - provided by Sofin. Note, that there's an issue related to this feature at the moment: https://github.com/HardenedBSD/hardenedBSD/issues/305.

* [Unbound](https://www.nlnetlabs.nl/projects/unbound/about/) DNS server is installed by default - configured as caching nameserver - used by all jails.

* [Nginx](http://nginx.org/) proxy is installed by default - used to proxy external domains to local ones (by name, using local caching DNS).



## A few pictures are better than thousand words!

---

![Pic 1](http://s.verknowsys.com/6d9fc9ef40f539d28f25710a094e3b4f.png)
> Pic 1. For this example I've set: `default_jails_domain='myhost.my-shiny.com'` when invoking `setup-dedicated-system` script, below is list of invoked commands with commentary:
```

#
# This part is invoked on host system (red "λ" = superuser):
#

# Create new jail with given local IP address:
gvr create myhost 172.16.3.31

# Show detailed info about newly created jail:
gvr status myhost

# To login to new jail system directly from host system:
gvr login myhost


#
# This part is invoked in jail (blue "λ" = regular user: `worker`):
#

# Install Php72 software bundle with default configuration.
s i Php72
# Enable Php72 in environment.
s env +Php72

# Use it:
php --version
which php
which php-fpm

# Install latest Rust stable
s i Rust
# Enable Rust in environment.
s env +Rust

# Use it:
rustc --version
which cargo


```

---

![Pic 2](http://s.verknowsys.com/c0e1a6c260f73da19fb1bfa28ed895a6.png)
> Pic 2. Private ZFS datasets organization, full network stack in action - loopback is there (yay).
>        NOTE: newly created jails allocate no disk space since they're clones on deduplicated ZFS filesystem.

---

![Pic 3](http://s.verknowsys.com/04d32334ef8f3b55a969c7db820c5509.png)
> Pic 3. NOTE: My host system is called "cb2".
>        Set SSH public key as SSH access key for `myhost` jail (only [ED25519](https://ed25519.cr.yp.to/) keys are supported at the moment)
>        and login to jail over SSH (using my local OpenVPN connection with my private OpenVPN server).


![Pic 4](http://s.verknowsys.com/54d032a7b619392fa7ff29b170b59e93.png)
> Pic 4. Sofin-installer - help() invoked from my macOS workstation.

## My other projects that are part of the system:

* [Shable](https://github.com/VerKnowSys/Shable) - "Ansible" compliant software written in pure `sh`.

* [Sofin](https://github.com/VerKnowSys/sofin) - "Software Installer" also written in pure `sh`.

* [Sofin-definitions](https://github.com/VerKnowSys/sofin-definitions/) - Sofin software definitions - all pure `sh`.

* [svdOS](https://github.com/VerKnowSys/svdOS) - this repo, with: [system build script](https://github.com/VerKnowSys/svdOS/blob/master/build-all), [production kernel configuration](https://github.com/VerKnowSys/svdOS/blob/master/kernel/VerKnowSys-11), other [configuration files](https://github.com/VerKnowSys/svdOS/tree/master/etc) and [system installer and updater script](https://github.com/VerKnowSys/svdOS/blob/master/svdup) - majority in pure `sh` as well.


## But… why?

* I've learned a lot about systems, software hardening and security.

* It's a challenge and this is my PoC of "how to provide modern, concise and clean open-source server-system".

* I wish to do a server system, which will get as close in quality as macOS is for workstation systems.

* It took me 7 years to implement all the stuff, so I guess it's a lot of fun too ;)


## License

BSD/MIT/IDGAF


