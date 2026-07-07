---
up:
  -
  - "[[01 Choosing an OS]]"
---

Proxmox is a Debian-based Linux distribution designed for running virtual machines. It is suited well for home labs because of it's friendly web interface and big community. We are going to set up a single node (which is what Proxmox calls a physical server).

## Installing Proxmox

Grab a USB stick labelled Proxmox or [download an image](https://proxmox.com/en/downloads/proxmox-virtual-environment) and flash your own. Plug in the USB stick into your device and boot from it. If you don't know how to do this, search for "`<YOUR-DEVICE>` bios" or "`<YOUR-DEVICE>` change boot order", or ask us. If you did everything correctly, you should see the installer:

![PVE installer](images/proxmox-install-00.27.png)

Select "Install Proxmox VE (Graphical)" and hit Enter. Accept the EULA. Proxmox is free and open-source software under the AGPLv3, but there is an enterprise subscription.

![Proxmox EULA screen](images/proxmox-install-01.35.png)

Now, select your drive. Ensure to select the drive you want to use as a boot drive, you can set up additional drives later. The USB thumb drive you're booting off should not show up here.

![Proxmox drive selection screen](images/proxmox-install-01.58.png)

The next step is probably self-explanatory.

![Proxmox timezone and keyboard layout screen](images/proxmox-install-02.16.png)

This step is important. Set up a secure password for the root user (you can change this later) and a valid email (you will receive automated messages from your server like backup failures there).

![Proxmox root password and email screen](images/proxmox-install-02.26.png)

Then, set up networking. You will have to change this when you take the server home, but for now, it should look like this. Change the hostname from whatever it currently is to a unique one, like `<YOURNAME>-server`. Also ensure to set the DNS server[^1] to a public one, either `1.1.1.1` (Cloudflare) or `9.9.9.9` (quad9, European DNS server)

![Proxmox network configuration screen](images/proxmox-install-03.14.png)

Now, a summary will show. Ensure the settings are correct (not as in this screenshot though), then click Install.

![Proxmox installation summary screen](images/proxmox-install-03.57.png)

Your device will now reboot. When it does, unplug the USB stick and wait for the device to fully boot. You should see a screen like this:

![Proxmox boot screen after installation](images/proxmox-install-finished.jpeg)

Enter the URL in your browser and ignore an invalid certificate prompt if shown.

## Setting up Proxmox

Since we want to run Docker on our Proxmox machine, we recommend installing a Debian LXC on Proxmox and running Docker on that instance.

> [!info] About this setup
> Running Docker inside an LXC is not fully recommended. Officially, it is preferred to run Docker inside a full VM. However, this is unnecessary in our case, as the overhead is much higher for no reason.

Begin by logging in into Proxmox with the username `root` and the password you set up. For realm, ensure PAM is selected. [^2]

## Post-installation maintenance

Before we can continue, we are going to run a postinstall script for some sensible defaults. For that, click on your host in the left tree and on the top bar on the right, click shell. This should open a terminal.

We are going to run the "PVE Post Install" script from <https://community-scripts.org/>, a reputable repository of scripts to simplify installation and maintenance of services on Proxmox. You can find the script on <https://community-scripts.org/scripts/post-pve-install>. Run the following command:

```sh
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
```

You will now be asked a few questions. Press y or n to answer, and enter to continue. Answer the questions like this:

- Yes to starting
- If asked about "deb822 sources", click OK
- Disable the pve-enterprise repository (that's only for paying customers)
- Disable ceph-enterprise (that's if you have multiple servers)
- Enable pve-no-subscription (for Proxmox updates if you are not a paying customer)
- Disable ceph (as before)
- Don't add pvetest (that's for beta testing)
- Disable the subscription nag if you want to
  - If you selected Yes, click Ok on the next screen
- Disable high availability and Corosync (unless you plan to use multiple servers running Proxmox in your home network)
- Update Proxmox now (takes some time)

The output should look like this:

```text
    ____ _    ________   ____             __     ____           __        ____
   / __ \ |  / / ____/  / __ \____  _____/ /_   /  _/___  _____/ /_____ _/ / /
  / /_/ / | / / __/    / /_/ / __ \/ ___/ __/   / // __ \/ ___/ __/ __ `/ / /
 / ____/| |/ / /___   / ____/ /_/ (__  ) /_   _/ // / / (__  ) /_/ /_/ / / /
/_/     |___/_____/  /_/    \____/____/\__/  /___/_/ /_/____/\__/\__,_/_/_/

 ✓ Disabled 'pve-enterprise' repository
 ✓ Disabled 'ceph enterprise' repository
 ✓ Added 'pve-no-subscription' repository
 ✓ 'ceph' package repository (no-subscription) already exists (skipped)
 ✗ Selected no to Adding 'pvetest' repository
 ✓ Disabled subscription nag (Delete browser cache)
 ✓ Disabled high availability
 ✓ Disabled Corosync
 - Updating Proxmox VE (Patience)...
```

After the update, you are asked to reboot now, you should probably do so.

## Getting the Debian template

1. In the left tree, select your node -> `local` storage.
2. Click the **CT Templates** tab
3. Click the **Templates** button (top)
4. In the search box, type "debian"
5. Select **debian-13-standard** and click **Download**

![Proxmox templates listing](images/proxmox-1.png)

Wait for the task to finish (last line is "TASK OK"), then close the window:

![Proxmox task log showing TASK OK](images/proxmox-2.png)

## Create the container

From the Proxmox web GUI, click the blue Create CT button in the top-right corner. Tab by tab:
Set the hostname to `docker`, ensure the CT ID is set to 100 and that "Unprivileged container" and "Nesting".

Then, paste your public key, located in `<YOUR-USER-PATH>/.ssh/id_ed25515.pub` or `<YOUR-USER-PATH>/.ssh/id_rsa.pub` and click Next. The public key is the whole line, including the `ssh-ed25519` or `ssh-rsa` prefix and the `user@host` at the end of the line, not just the random string inbetween.

![Create CT public key screen](images/proxmox-create-ct-50.35.png)

Now, select the Debian 13 template you just downloaded and click Next.

![Create CT template selection screen](images/proxmox-create-ct-04.46.png)

For the disk, enter a capacity of at least 32 GiB:

![Create CT disk size screen](images/proxmox-create-ct-04.55.png)

For cores, enter 2 or 4 (depending on if you have more than 4 cores):

![Create CT CPU cores screen](images/proxmox-create-ct-05.23.png)

In the Network tab, check DHCP for IPv4 and IPv6.

![Create CT network screen](images/proxmox-create-ct-06.44.png)

For DNS, click next.

![Create CT DNS screen](images/proxmox-create-ct-07.34.png)

Then, ensure "Start after created" is unchecked and click Finish.

![Create CT confirmation screen](images/proxmox-create-ct-07.41.png)

## Unfuck the container

Now, since Docker is very peculiar with permissions, we need to edit the container privileges. For that, click on your host in the left tree and on the top bar on the right, click shell. This should enter a Terminal on the _host_. Open the container's configuration in a text editor:

```bash
nano /etc/pve/lxc/100.conf
```

(if you need help with `nano`, see [[How to nano]])

Append the line:

```conf
lxc.apparmor.profile: unconfined
```

This decreases security checks in the LXC, which can clash with Docker trying to create its own containers.

## Starting it

In the right tree, select the newly created LXC and click start.

![[images/proxmox-install-31.58.png|Starting the LXC container]]

Now, you can continue with [[02 Connecting via SSH]].

## Taking it home (do this at the end of the workshop)

Before you can take your device home, you should change the network configuration so you can just plug it in. In the Proxmox UI. Go to your node on the left, then click Network, select `vmbr0` and click Edit.

![Network configuration for taking the device home](images/proxmox-take-home.png)

If your home router is for example 10.0.0.1 with a subnet mask of 255.255.0.0, the router's IP 10.0.0.1 for the gateway and for example 10.0.0.5/16 for the IPv4 (you can select any IP, but the CIDR is based on the subnet mask).

After editing, do not click Apply Configuration, and instead shut down the machine later. We still need to edit one file. Open a shell on the host as before, then edit `/etc/hosts`:

```sh
nano /etc/hosts
```

Edit the line containing your old IP address. In this example, change

```text
192.168.1.5 <NODE>.hpi.uni-potsdam.de <NODE>
```

to

```text
10.0.0.5 <NODE>.local <NODE>
```

Save and exit. You are now ready to shut down the device and plug it in at home. There, it should be reachable on the IP you just set.

[^1]: A DNS server is a service which translates names into IP addresses, for example hpi.de -> 141.89.225.126. This means that even though the DNS server cannot read you do on a specific website or app, it can read that you are using/visiting it.
    However, we want to use a public DNS resolver (instead of the one provided by the local network). This is because that way you can ensure which one is used instead of relying on whatever the network administrator has configured, which is usually 8.8.8.8, Google's DNS server.

[^2]: The realm is where the user is stored. PAM is the default user manager under most Linux systems. This means that there exists a Linux user account for every PAM account. The other realm you can select is Proxmox VE, which means that the user is only valid for Proxmox. However, the default `root` user is always a member of the PAM realm, not of Proxmox VE.
