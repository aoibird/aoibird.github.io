---
layout: post
title: Command line tools for service management in Linux
categories: ToolBox
tags: ["Linux", "CLI", "Service"]
---

When it comes to service management in Linux, the controversial role of systemd is undeniable. Despite the numerous critical arguments it has faced, systemd continues to be an essential component of Linux distributions.

## systemd

To manage a service under systemd, you can use the `systemctl` command, followed by a subcommand, and specify the name of the service. These service files are typically located in `/usr/lib/systemd` and `~/.config/systemd` and end with `.service` extention. To enable, disable, start, stop a service, you can use the following commands:
```bash
systemctl enable --now bluetooth.service # or bluetooth
systemctl start bluetooth.service
systemctl stop bluetooth.service
systemctl disable bluetooth.service
```

This is an example configuration; you can display its content by typing `systemctl cat --user gpg-agent`.
```conf
# /usr/lib/systemd/user/gpg-agent.service
[Unit]
Description=GnuPG cryptographic agent and passphrase cache
Documentation=man:gpg-agent(1)
Requires=gpg-agent.socket

[Service]
ExecStart=/usr/bin/gpg-agent --supervised
ExecReload=/usr/bin/gpgconf --reload gpg-agent
```
You can observe that the configuration is declarative; it involves specifying dependencies and issuing start or reload commands.

## System V


Before systemd, the traditional method for managing services was called System V. On older systems, you can use the following commands to start, stop, and restart a service:
```bash
service httpd start
service httpd restart
service httpd stop
```
The configuration file for these services are located in `/etc/init`, and they consist of scripts that define the steps required to start or stop a service. This is imperative, in contrast to systemd, which adopts a declarative approach to service configuration.

This is an example configuration located in `/etc/init/docker.conf`:
```conf
description "Docker daemon"

start on (filesystem and net-device-up IFACE!=lo)
stop on runlevel [!2345]

limit nofile 524288 1048576

# Having non-zero limits causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
limit nproc unlimited unlimited

respawn

kill timeout 20

pre-start script
	# see also https://github.com/tianon/cgroupfs-mount/blob/master/cgroupfs-mount
	if grep -v '^#' /etc/fstab | grep -q cgroup \
		|| [ ! -e /proc/cgroups ] \
		|| [ ! -d /sys/fs/cgroup ]; then
		exit 0
	fi
	# <......>
end script

script
	# modify these in /etc/default/$UPSTART_JOB (/etc/default/docker)
	DOCKERD=/usr/bin/dockerd
	DOCKER_OPTS=
	if [ -f /etc/default/$UPSTART_JOB ]; then
		. /etc/default/$UPSTART_JOB
	fi
	exec "$DOCKERD" $DOCKER_OPTS --raw-logs
end script

# Don't emit "started" event until docker.sock is ready.
# See https://github.com/docker/docker/issues/6647
post-start script
	DOCKER_OPTS=
	DOCKER_SOCKET=
	if [ -f /etc/default/$UPSTART_JOB ]; then
		. /etc/default/$UPSTART_JOB
	fi
	# <......>
end script
```


## Further reading

- [systemd - Arch Wiki](https://wiki.archlinux.org/title/systemd)


