---
up:
  - "[[How To Home Server]]"
---
Docker allows you to install containers that contain an operating system and a service / program. Containers are more secure than installing a service directly on your OS. They also fix the "works on my machine", which you may be familiar with :)

If you are using your laptop, jump to the section for your OS. Otherwise, go to [[#Linux (Debian / Proxmox / Raspberry Pi OS)]].

## Installing Docker

### Linux (Debian / Proxmox / Raspberry Pi OS)

Unfortunately, Docker is not available on Debian out of the box. Install it by running (as root)

```sh
apt update && apt install -yqq curl # install curl to download the script
```

Then, run the installation script:

```sh
curl -sSL https://get.docker.com/ | bash # install docker
```

### Linux (Other)

If you are using another distribution, ensure that `curl` is already installed. Then, run the same curl-bash-script as on Debian.

### macOS

Docker is available via Docker Desktop on macOS, but the performance is quite bad. Instead, you can use [OrbStack](https://orbstack.dev/), which uses a different engine, but is completely compatible otherwise.

### Windows

**Windows is a horrible choice to use as a server, this guide is only for testing! You should never run it as a home server unless you absolutely know what you're doing.**

The easiest way to install Docker on Windows is to install [Docker Desktop](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-win-amd64). Follow the install wizard and restart your PC if required.

[Docker Windows install guide](https://docs.docker.com/desktop/setup/install/windows-install/)

## Testing the installation

Try running

```bash
docker run --rm -it hello-world
```

You should get this output.

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete 
Digest: sha256:f9078146db2e05e794366b1bfe584a14ea6317f44027d10ef7dad65279026885
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

Also check Compose is installed by running

```bash
docker compose version
```

It should output

```
Docker Compose version <VERSION>
```

## Setting up your workspace

When using Compose, each service gets it's own folder containing a `compose.yaml` and optionally, the service data or configuration. We will put all of these folders in a folder we call the workspace. Our workspace is going to be located at `/srv`. If you are using your laptop, you can put this folder anywhere else, too.

Create the directory:

```sh
mkdir -p /srv
```

