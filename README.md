svdOS
=========

"Because you deserve to be ServeD!"


## Manual installation:

0. Install HardenedBSD 64bit 11.0+. If you wish to install svdOS on local vm created with ZFS and xHyve, you may like to [follow steps from README here](https://github.com/VerKnowSys/xhyve).

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

5. Clone [Shable](https://github.com/VerKnowSys/Shable): `git clone https://github.com/VerKnowSys/Shable`,

6. Do: `cd Shable && echo "myhostname ip=11.22.33.44 remote_vpn_server=22.33.44.55" >> inventory`. You can also override default internal network which is: `172.16.3` to own one, by passing additional argument: `vpn_network=172.16.123`.

7. Do `./setup-dedicated-system myhostname` to complete system installation.
