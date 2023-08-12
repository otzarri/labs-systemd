# labs-systemd

Doc and code about systemd.

# Hostname

Get the system hostname and extra information about hardware and OS:

```
$ hostnamectl 
```

Set a specific hostname or FQDN:

```
$ hostnamectl set-hostname <hostname-or-fqdn>
```

# Timezone

Print date and time information:

```
$ timedatectl
```

List timezones:

```
$ timedatectl list-timezones
```

Set timezone:

```
$ timedatectl set-timezone Europe/London
```

# Shutdown and reboot

Systemd reimplemented the shutdown and reboot.

To shutdown:

```
sudo systemctl poweroff
```

To reboot:

```
sudo systemctl reboot
```

> 💡 systemd can't schedule shutdowns or reboots for the future. The solution is to use the old `poweroff` or `shutdown` commands of your distro.

# Service management

> 💡 [chkservice](https://github.com/linuxenko/chkservice) can help you managing services.

`systemctl` has replaced the old daemon management utilities.

`systemctl` commands are consistent across the various Linux distro families.

No more `init` scripts. But backwards compatibility is provided for third-party daemons that are not systemd-ready.

## Check services and status

List all the services:

```
sudo systemctl list-unit-files --type service
```

Check the status of a specific service:

```
sudo systemctl status <name>.service
```

Some services give additional information using the `-l` option:

```
sudo systemctl -l status <name>.service
```

## Disabling and enabling services

When services are enabled, they start with the operating system. Conversely, when they are disabled, they do not. Before `systemd`, each distribution managed it in its own way: with `service`, `chkconfig`, `update-rc.d`, etc.

Enable service:

```
sudo systemctl enable <name>.service
```

Disable service:

```
sudo systemctl disable <name>.service
```

Check if the service is enabled:

```
sudo systemctl is-enabled <name>.service
```

## Stopping and starting services

Stop service:

```
sudo systemctl stop <name>.service
```

Start service:

```
sudo systemctl start <name>.service
```

Restart service:

```
sudo systemctl restart <name>.service
```

Check if the service failed:

```
sudo systemctl is-failed <name>.service
```

## Automatically restart crashed services

In the unit file, under `[Service]` section add `Restart=always`.

After that, reload the `systemd` service:

```
$ sudo systemctl daemon-reload
```

# Override unit files

To override existing unit files, add the new files under `/etc/systemd/system`. This work is managed by `systemctl` so we don't have to handle with text files.

To edit the service section of a unit file execute:

```
$ sudo systemctl edit --full <name>.service 
```

To see the changes after editing the override file, run:

```
$ sudo systemctl cat <name>.service 
```

To apply the changes, reload `systemd`:

```
$ sudo systemctl daemon-reload
```

# Targets

Targets are for `systemd` what runlevels are for Init V.

List active targets:

```
sudo systemctl list-units --type target
```

List all the targets:

```
sudo systemctl list-units --type target --all
```

There are lots of targets in `systemd`, but seven of them do correlate to the different SysVInit runlevels:

| Runlevel | Target            | Description                             |
| -------- | ----------------- | --------------------------------------- |
| 0        | poweroff.target   | Shut down and power off the system.     |
| 1        | rescue.target     | Set up a rescue shell.                  |
| 2        | multi-user.target | Set up non-graphical multi-user system. |
| 3        | multi-user.target | Set up non-graphical multi-user system. |
| 4        | user.target       | Set up non-graphical multi-user system. |
| 5        | graphical.target  | Set up a graphical multi-user system.   |
| 6        | reboot.target     | Shut down and reboot the system.        |

The default target will normally be set to either `multi-user.target` or `graphical.target`.

Get the default target:

```
sudo systemctl get-default
```

Set a default target:

```
sudo systemctl set-default <name>.target
```

Go to a runlevel:

```
sudo systemctl isolate <name>.target
```

# Systemd unit file

A systemd unit file has three sections:

| Section | Description                                                                                                                                         |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Unit    | The informational section. Description, and what other services need to start befire this one can start.                                            |
| Service | Contains the commands and any environmental variables needed to run the service. It can also contain a command to gracefully shut down the service. |
| Install | Which target wants or requires this service? (Typically, either graphical or multi-user). Can also be used to define aliases for the service.       |

To get more information about `systemd` unit files, run the command below:

```
 $ man systemd.service
```

# Logging

Get logs:

```
sudo journalctl
```

Get logs of a specific unit:

```
sudo journalctl -u <name>
```

Get tail-style logs of a specific unit:

```
sudo journalctl -f -u <name>
```

Get logs since a specific date:

```
sudo journalctl --since "2020-12-28 10:24:00"
```

List the times the system was booted:

```
sudo journalctl --list-boots
```

Get logs of a specific program:

```
sudo journalctl _COMM=firefox
```

Get logs of the kernel:

```
sudo journalctl -k
```

Get logs of a specific device:

```
sudo journalctl /dev/sda
sudo journalctl /dev/sda1
```
