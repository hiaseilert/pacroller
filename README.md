# Pacroller
The "Unattended Upgrades" for Arch Linux.

## Concept
Parsing the output of pacman and pacman.log, searching for known patterns and notifying the user whether there is a potential error.
Currently the design is regex-based, any output that is unable to match a set of regex is reported back to the user.

## Installation
`yay -S pacroller`

## Usage
pacroller has the following subcommands
```
run [-d --debug]
    start an upgrade
    if the upgrade fails or pacroller determines that human action is required,
    pacroller writes an exception to the status database, and refuses to run again
    without resetting its failure status.
status [-v --verbose] [-m --max <number>]
    print details of a previously successful upgrade
reset
    reset the current failure status
test-smtp
    send an test email to the configured address
```
There is also a systemd timer for scheduled automatic upgrades.

## Configuration
Pacroller reads `/etc/pacroller/config.json` on startup.
### custom sync commands
Pacroller can be configured to use custom sync commands, which allows the usage of a different set of mirrors when syncing the database. Enable the "custom_sync" option and write your custom `/etc/pacroller/sync.sh`.
### needrestart
If the "needrestart" option is enabled, needrestart should be called after a successful upgrade.
### hold packages
Put your hold packages in a json keyval {package name: regex}, where the regex should have at least one matching group.
If pacroller observes any changes of the matching group or the hold package is to be removed, it refuses to upgrade further.
### ignored pacnew
A list of pacnew files that are silently ignored during parsing, any other pacnews will trigger a warning and prevent further upgrades.
### custom pacman hooks and packages
Custom pacman hooks and packages output matching is configurable via `/etc/pacroller/known_output_override.py`.
### check systemd status
The "systemd-check" option allows pacroller to check fo degraded systemd services before an upgrade.
### clear package cache
Pacroller wipes /var/cache/pacman/pkg after a successful upgrade if the option "clear_pkg_cache" is set.
### save pacman output
Every time an upgrade is performed, the pacman output is stored into /var/log/pacroller. This can be configured via the "save_stdout" keyword.

## Smtp
Configure `/etc/pacroller/smtp.json` to receive an email notification when an upgrade fails. Note that pacroller will not send any email if stdin is a tty (can be overridden by the `--interactive` switch).

## Limitations
- Your favourite package may not be supported, however it's easy to add another set of rules.
- Restarting the whole system after a kernel upgrade is not implemented.
- Human interaction is required occasionally.
- Does not check news from archlinux.org
