Sample init scripts and service configuration for nyand
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/nyand.service:    systemd service unit configuration
    contrib/init/nyand.openrc:     OpenRC compatible SysV style init script
    contrib/init/nyand.openrcconf: OpenRC conf.d file
    contrib/init/nyand.conf:       Upstart service configuration file
    contrib/init/nyand.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "nyan" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes nyand will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, nyand requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, nyand will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that nyand and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If nyand is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running nyand without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/nyan.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/nyand`  
Configuration file:  `/etc/nyan/nyan.conf`  
Data directory:      `/var/lib/nyand`  
PID file:            `/var/run/nyand/nyand.pid` (OpenRC and Upstart) or `/var/lib/nyand/nyand.pid` (systemd)  
Lock file:           `/var/lock/subsys/nyand` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the nyan user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
nyan user and group.  Access to nyan-cli and other nyand rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/nyand`  
Configuration file:  `~/Library/Application Support/NYAN/nyan.conf`  
Data directory:      `~/Library/Application Support/NYAN`
Lock file:           `~/Library/Application Support/NYAN/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start nyand` and to enable for system startup run
`systemctl enable nyand`

4b) OpenRC

Rename nyand.openrc to nyand and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/nyand start` and configure it to run on startup with
`rc-update add nyand`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop nyand.conf in /etc/init.  Test by running `service nyand start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy nyand.init to /etc/init.d/nyand. Test by running `service nyand start`.

Using this script, you can adjust the path and flags to the nyand program by
setting the NYANCOIND and FLAGS environment variables in the file
/etc/sysconfig/nyand. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.nyan.nyand.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.nyan.nyand.plist`.

This Launch Agent will cause nyand to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run nyand as the current user.
You will need to modify org.nyan.nyand.plist if you intend to use it as a
Launch Daemon with a dedicated nyan user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
