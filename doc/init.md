Sample init scripts and service configuration for acrd
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/acrd.service:    systemd service unit configuration
    contrib/init/acrd.openrc:     OpenRC compatible SysV style init script
    contrib/init/acrd.openrcconf: OpenRC conf.d file
    contrib/init/acrd.conf:       Upstart service configuration file
    contrib/init/acrd.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three startup configurations assume the existence of a "acr" user
and group.  They must be created before attempting to use these scripts.

2. Configuration
---------------------------------

At a bare minimum, acrd requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, acrd will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that acrd and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If acrd is run with "-daemon" flag, and no rpcpassword is set, it will
print a randomly generated suitable password to stderr.  You can also
generate one from the shell yourself like this:

bash -c 'tr -dc a-zA-Z0-9 < /dev/urandom | head -c32 && echo'

Once you have a password in hand, set rpcpassword= in /etc/acr/acr.conf

For an example configuration file that describes the configuration settings,
see contrib/debian/examples/acr.conf.

3. Paths
---------------------------------

All three configurations assume several paths that might need to be adjusted.

Binary:              /usr/bin/acrd
Configuration file:  /etc/acr/acr.conf
Data directory:      /var/lib/acrd
PID file:            /var/run/acrd/acrd.pid (OpenRC and Upstart)
                     /var/lib/acrd/acrd.pid (systemd)

The configuration file, PID directory (if applicable) and data directory
should all be owned by the acr user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
acr user and group.  Access to acr-cli and other acrd rpc clients
can then be controlled by group membership.

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists on just copying it to
/usr/lib/systemd/system directory, followed by the command
"systemctl daemon-reload" in order to update running systemd configuration.

To test, run "systemctl start acrd" and to enable for system startup run
"systemctl enable acrd"

4b) OpenRC

Rename acrd.openrc to acrd and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
"/etc/init.d/acrd start" and configure it to run on startup with
"rc-update add acrd"

4c) Upstart (for Debian/Ubuntu based distributions)

Drop acrd.conf in /etc/init.  Test by running "service acrd start"
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon uitility.

4d) CentOS

Copy acrd.init to /etc/init.d/acrd. Test by running "service acrd start".

Using this script, you can adjust the path and flags to the acrd program by
setting the AcrD and FLAGS environment variables in the file
/etc/sysconfig/acrd. You can also use the DAEMONOPTS environment variable here.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
