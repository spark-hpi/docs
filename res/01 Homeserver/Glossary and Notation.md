---
up:
  - "[[How To Home Server]]"
---
## Glossary

| Term                                | Acronym                                                  | Definition                                                                                                                                                                                                                                                     |
| ----------------------------------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Distribution, Distro                |                                                          | An operating system based on Linux. As Linux itself is just a kernel, we need essential services and software like the init system or a package manager to make it an actual, usable operating system.                                                         |
| Debian                              |                                                          | A general-purpose Linux distribution often used for servers. Probably the biggest one.                                                                                                                                                                         |
| Debian derivatives                  |                                                          | A distribution based on Debian. Examples include Proxmox, Raspberry Pi OS and Ubuntu.                                                                                                                                                                          |
| Proxmox                             |                                                          | A Debian derivative designed to run virtual machines.                                                                                                                                                                                                          |
| SSH                                 | Secure SHell                                             | A protocol to execute terminal commands on another machine over the network.                                                                                                                                                                                   |
| APT                                 | Advanced Package Tool                                    | The package manager used on Debian distribution                                                                                                                                                                                                                |
| root                                |                                                          | A special user on Linux (and Linux-like) operating systems which has every permission. Most administrative commands like installing packages or changing global settings have to be executed as root. Sometimes also called the "super user" or "super admin". |
| sudo                                | super/switch user do                                     | A command to run a command as root (or as another user).                                                                                                                                                                                                       |
| `~`, `$HOME`,<br>`<YOUR-USER-PATH>` |                                                          | Your user folder. On Linux, this is `/home/<USERNAME>`, on macOS `/Users/<USERNAME>` and on Windows `C:\Users\<USERNAME>`. On macOS and Windows, they are not shown under "important locations" / "favourites" by default.                                     |
| nano                                |                                                          | A text editor for the terminal.                                                                                                                                                                                                                                |
| YAML                                | Yet Another Markup Language / YAML Ain't Markup Language | A structured data language usually used for configuration files. Indicated by the `.yml` or `.yaml` file extensions.                                                                                                                                           |
| Virtualisation                      |                                                          | The practice of running an isolated operating system on another for security and/or compatibility reasons.                                                                                                                                                     |
| Virtual machine                     |                                                          | A virtualized computer, with it's own (virtual) devices and ports running a full operating system.                                                                                                                                                             |
| Container                           |                                                          | A type of virtualisation on Linux where the host and the container share one kernel and all devices, making them much more lightweight than running a full virtual machine.                                                                                    |
| (Docker) Compose                    |                                                          | A set of commands which will create containers based on a `compose.yaml`.                                                                                                                                                                                      |
| LXC                                 |                                                          |                                                                                                                                                                                                                                                                |


## Variable notation

We use a common notation for placeholders. `<NAME>` is the required variable NAME, which you have to replace with the name needed (context should usually tell you what name we are referring to). When replacing it, replace not only the the variable name, but also the  brackets/braces _directly_ around it. For example, if your (user-)name is "alice" and your server has the hostname `alice-super-pc`, you should enter a command `ssh <YOURNAME>@<HOSTNAME>` like `ssh alice@alice-super-pc`, and a command `echo '<<YOURNAME>>' >> wonderland.txt`like `echo '<alice>' >> wonderland.txt`. 

## File content notation

For files you need to create, we usually put the file path into the first line. For example, if you need to create the files `/etc/foo.txt`, `/etc/bar.txt` and `/etc/baz.txt`, the notation

```
# /etc/foo.txt
bar
```

```
# /etc/bar.txt
baz
```

```
# /etc/baz.txt
foo
```

gives you the exact file name per content. When creating the file, omit these comments.