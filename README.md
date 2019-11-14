ServeD-OS
=========

"Because you deserve to be ServeD!" - an open source server-system designed on top of [HardenedBSD](https://hardenedbsd.org/) and [ZFS](http://www.open-zfs.org/wiki/Main_Page).



## Authors:

* Daniel ([dmilith](https://twitter.com/dmilith)) Dettlaff (2011-…)



## Custom/ manual installation:

0. Install 64bit [HardenedBSD](https://hardenedbsd.org/) or [FreeBSD](https://www.freebsd.org/) with version: `11.2+`. If you wish to install svdOS on local vm created with [ZFS](http://www.open-zfs.org/wiki/Main_Page) and [xHyve](https://github.com/mist64/xhyve), you may like to [follow steps from README here](https://github.com/VerKnowSys/xhyve).

1. Run: `fetch -o - https://raw.githubusercontent.com/VerKnowSys/svdOS/master/svdup | sh` - to install ServeD release/update on vanilla system, or update older system version, to current stable build.

2. After `reboot` new system will be booted automatically (`beadm list` to list all available boot environments).



## Dedicated machine installation:


### System preparation:

> NOTE: I use: [EX-LINE](https://www.hetzner.com/dedicated-rootserver/matrix-ex) server type, (Ryzen doesn't work with FreeBSD 11-STABLE).

0. Login to: [Hetzner Robot](https://robot.your-server.de/), select your server on servers list.

1. Activate rescue system with version `FreeBSD 11.1`. Reboot system from web panel: `CB2->Reset->Execute an automatic hardware reset`. Confirm, wait ~48s (default boot time 30s + ~18s for kernel).

2. Login to rescue system via SSH using provided root password: `ssh root@YOUR-SRV-IP`.

3. Run `bsdinstallimage` (script provided by Hetzner). Follow steps:
    - Pick `FreeBSD 11.2`, `64bit`,
    - Continue with default `keymap`,
    - Enter desired `myhostname`,
    - Unselect all system components on list,
    - Pick `Auto (ZFS)` option:
        - Pool disk - use `mirror`, with both disks (`ada0` + `ada1`),
        - Use defaults for other ZFS settings,
        - Hit `Install`, confirm that we wish to create new pool,
    - Installation shouldn't take more than a minute. After that type new root password,
    - Select default network card (`em0`), don't use `DHCP`, but `manual` for `IPv4` - use predefined values,
    - Select "NO" when asked about `UTC`, pick "Europe", "Poland", confirm it's "CEST",
    - Skip date/time settings,
    - Enable only: `sshd`, `ntpd`, and `dumpdev` system services,
    - Select all system-hardening options on list, but "Disable process debugging facilities for unprivileged users",
    - Don't add any system users,
    - Hit `Exit` from menu, confirm that you wish to open terminal - we need to do few additional modifications of base system configuration,
    - Paste these commands in terminal followed by `Enter`:

    ```
    sed -i '' -e 's|#PermitRootLogin no|PermitRootLogin yes|' /etc/ssh/sshd_config
    mkdir -p ~/.ssh
    cat > ~/.ssh/authorized_keys
    ```

    - Paste your SSH public key(s), followed by `Enter`. Hit `Ctrl-d` to save `authorized_keys` file,
    - That's all. Now hit `Ctrl-d` (or type `exit`) to quit Shell.
    - Installation is now complete!

4. When `bsdinstallimage` is done, type `reboot`, to reboot to freshly installed `FreeBSD-11.2` (vanilla) system. NOTE: This system is used only once - to bootstrap [svdup installer](https://github.com/VerKnowSys/svdOS/blob/master/svdup) - but it's required (as system configuration resource).


### Post installation - Shable tasks:

5. On your local workstation - clone [Shable](https://github.com/VerKnowSys/Shable): `git clone https://github.com/VerKnowSys/Shable`.

6. Do: `cd Shable && echo "myhostname ip=11.22.33.44 remote_vpn_server=22.33.44.55 vpn_network=172.16.123 default_jails_domain='mydomain.com'" >> inventory`. NOTE: Currently only `172.16.0.0/16` subnet is supported for VPN networks (65535 hosts max).

    * `ip` -> External IP address of the dedicated machine (mandatory),

    * `remote_vpn_server` -> External IP address of OpenVPN service (mandatory),

    * `default_jails_domain` -> Domain used as default one for jails (mandatory),

    * `vpn_network` -> Override default VPN network (default is: `172.16.3`) (optional),


7. Generate new passwordless OpenVPN key and certificate for your new dedicated server. Put `ca.crt` (generated for "your OpenVPN server") + `your-inventory-host-name.crt` + `your-inventory-host-name.key` files under `templates/vpn/` directory.

8. Do `./setup-dedicated-system myhostname` to complete system installation. NOTE: Default SSH config file (`~/.ssh/config` ) will be updated with `myhostname` entry after installation.

9. After about a minute, type: `ssh myhostname` to login as superuser to remote system.

10. Use `gvr` (Governor) to maintain your jails. Use `s` (Sofin) to install server software.



## Built in features:

* ServeD system implies, that a single dedicated machine is part of a "distributted infrastructure". By default, [OpenVPN](https://openvpn.net/) is used to provide internal network access for all jails automatically. NOTE: It's necessary to provide your private VPN keys. Without them installation will not be possible at the moment (overridable - maybe even supported one day).

* Hardened kernel and base system (prebuilt with support for all [HardenedBSD features](https://hardenedbsd.org/content/easy-feature-comparison)).

* Support for full network stack under jails ([VIMAGE](https://wiki.freebsd.org/VIMAGE)).

* Preconfigured [PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html) to [NAT](https://en.wikipedia.org/wiki/Network_address_translation) external network for jails.

* Root filesystem (`/`) mounted read-only by default (overrideable).

* Software datasets read-only by default (overrideable).

* Limited superuser access to base system only. Listening to ports <1024 is allowed for jail default user (`worker`).

* Jailed user (`worker`), has full control over owned ZFS-datasets. Features allowed by default: `xattr,atime,casesensitivity,checksum,copies,logbias,primarycache,secondarycache,snapdir,userused,dedup,mountpoint,canmount,userprop,create,destroy,snapshot,rollback,clone,promote,rename,mount,send,receive,reservation,readonly`.

* Preconfigured ZSH Shell with cherry picked modules from [Oh My ZSH](https://github.com/robbyrussell/oh-my-zsh).

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

# Create new jail. Local IP address will be auto-picked from pool of local addresses:
gvr create myhost

# Show detailed info about newly created jail:
gvr status myhost

# To login to new jail system directly from host system:
gvr login myhost


#
# This part is invoked in jail (blue "λ" = regular user: `worker`):
#

# Install Php72 software bundle with default configuration.
s i Php72

# Use it:
php --version
which php
which php-fpm

# Install latest Rust stable
s i Rust

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

---

![Pic 4](http://s.verknowsys.com/95f121eeb3143b6aaf5c9e1fd2dd320f.png)
> Pic 4. Sofin-installer - help() invoked from my macOS workstation.

---

![Pic 5](http://s.verknowsys.com/ed6bfaa75afe3048acbbeeee3b01b72a.png)
> Pic 5. Governor (`gvr`) commands and jail attributes listed.

---

![Pic 6](http://s.verknowsys.com/5a0caff1a8403d29af591181d952d5f4.png)
> Pic 6. List boot environments. NOTE: `default` is our vanilla FreeBSD-11.2.

---

![Pic 7](http://s.verknowsys.com/661aa7fd4393313296458b30a5dac682.png)
> Pic 7. Each Sofin bundle has own immutable list of dependencies.

---

![Pic 8](http://s.verknowsys.com/a9c8cae6d6363333ae8e804c00b01263.png)
> Pic 8. Limit CPU per jail example: `myhost` to `40%` of a single CPU core. NOTE: `pcpu` can be grater than `100`. For example: `pcpu=200` means that software will be limited to use `2x100%` CPU cores max.

---

![Pic 9](http://s.verknowsys.com/d924a95e38a833f19411dbc5b4e75acf.png)
> Pic 9. There are several quota attributes - here we're setting three of them, each with immediate effect. Attributes are immediately applied on working jails. NOTE: Try: `gvr available` - to get list of all supported attributes.

--

![Pic 10](http://s.verknowsys.com/d503421c433336e6a65502bbc34c0cc3.png)
> Pic 10. Software version switching (and environment management) for an example of Php.


## Few words about /Shared/ mounts:

* `/Shared/Cache/*` - Dataset used as a system cache dir. Usually contains file snapshots from ServeD release process.

* `/Shared/IPs/*` - Contains pool of files of unique IPv4 address of the local (VPN) network.

* `/Shared/NetIDs/*` - Contains pool of files of unique Net-IDs (also serves as UID of worker user in Cell, and epair network interface number).

* `/Shared/DNS/*.conf` - DNS dataset with custom Unbound `*.conf` (file per zone) DNS configurations.

* `/Shared/Igniters/*.igni` - Dataset contains service igniters, f.e. `vpn.igni` will be invoked later with `igni vpn some-command` (for both regular and super user).

* `/Shared/Prison/*` - Dataset contains two datasets:

    * `/Shared/Prison/Cells/*` - Dataset with jail cells.

    * `/Shared/Prison/Sentry/*` - Dataset with metadata and configuration of each jail cell.



## Known issues/ problems:

* Only SSH ED25519 pub-keys are supported at the moment.

* After installation default SSH port (22) is switched to ServeD SSH port (60022).

* `setup-dedicated-system` manages `~/.ssh/config` entries automatically - in case of interruption or crash caused by an error - it doesn't invoke any cleanup, hence config has to be fixed manually.


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


