---
up:
  - "[[How To Home Server]]"
---
## Creating an SSH config

Then, create an SSH config file, a file named `config` (no extension) in the `<YOUR-USER-PATH>/.ssh` folder in your PC's home:

```
# ~/.ssh/config

# an alias so you can `ssh <NAME>`, e.g. server or raspi
Host <NAME>
   HostName <IP>
   User root  
   Port 22
```


> [!NOTE] IP on Proxmox
> Use the IP of the LXC you created.

## Logging in

Now, try running 

```sh
ssh <NAME>
```

You should be able to log in _without a password_. If not, check the OS installation guide again or ask for help.
## Securing the SSH server

Once your SSH key is on the server, we're going to disable password authentication for security reasons. Log into the server as root, then run

```sh
nano /etc/ssh/sshd_config
```

This will open up the SSH server configuration file in the nano text editor, see [[How to nano]]. Move around using arrow keys to find the line that says

```
#PasswordAuthentication yes
```

and change it to

```
PasswordAuthentication no
```

Then, find the line that starts with

```
#PermitRootLogin
```

and change it to

```
PermitRootLogin prohibit-password
```

Then, hit `ctrl/control + x` and press `y` and then enter to save the file. Then, run

```sh
sshd -t && systemctl restart sshd
```

to apply your changes. In a new terminal window, run `ssh <NAME>`. If that works, then good job: you didn't fuck it up! If not, talk to us and do not close the existing window, to not lock yourself out.