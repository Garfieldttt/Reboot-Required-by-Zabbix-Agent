# Debian/Ubuntu Reboot Required

## Overview

Detects a pending reboot on Debian and Ubuntu systems by checking for the file
`/var/run/reboot-required`, which the package manager creates after updates that
need a restart (for example a new kernel). Uses the Zabbix Agent in active mode.

## Requirements

- Zabbix Server/Agent 7.0
- Zabbix Agent 2 in active mode. `ServerActive` and `Hostname` must be set in
  `zabbix_agent2.conf` and match the host name in Zabbix.
- The template package `update-notifier-common` (Debian/Ubuntu) creates the
  `/var/run/reboot-required` file. Most desktop and cloud images ship it by
  default; on minimal servers install it if the file never appears:
  `apt install update-notifier-common`.

## Macros used

There are no macros in this template.

## Template links

There are no template links in this template.

## Discovery rules

There are no discovery rules in this template.

## Items collected

|Name|Description|Type|Key|
|----|-----------|----|---|
|Reboot required|Whether `/var/run/reboot-required` exists (1 = reboot pending, 0 = not required).|UNSIGNED|vfs.file.exists[/var/run/reboot-required]|
|Reboot required - packages|Packages that triggered the reboot-required flag.|TEXT|vfs.file.contents[/var/run/reboot-required.pkgs]|

Both items are active checks with a 1 hour interval.

Note on the packages item: `/var/run/reboot-required.pkgs` only exists while a
reboot is pending. When no reboot is required the item turns to a "Not supported"
state and the agent logs a "No such file or directory" message. This is expected
and resolves on its own once a reboot becomes pending. If the noise bothers you,
remove this item and keep only the exists check.

## Triggers

|Name|Expression|Priority|
|----|----------|--------|
|Reboot required on {HOST.NAME}|last(/Debian Reboot Required/vfs.file.exists[/var/run/reboot-required])=1|WARNING|

The trigger allows manual close.

## Value maps

|Name|Value|Mapped to|
|----|-----|---------|
|Debian Reboot Required|0|not required|
|Debian Reboot Required|1|required|

## Graphs

There are no graphs in this template.
