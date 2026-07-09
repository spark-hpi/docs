---
up:
  - "[[How To Home Server]]"
---

Now that we are all on the same page, we can install our first service: Home Assistant, an all-in-one local smart home solution like Google Home or Apple HomeKit. You can use it to control and automate anything "smart" in your household, via a mobile app or a web interface.

![[images/phone.png|Home Assistant on Android|200]]

## Installation

> [!NOTE] macOS
> If you use macOS you can not use `/srv/homeassistant` because the `/` folder is protected. For the following to commands replace `/srv/homeassistant` with `~/srv/homeassistant`. This will create the folder in your home dir (`/home/USERNAME/srv/homeassistant`). you can also use a `~/.srv/homeassistant` to make the `srv` folder invisible.

First, create a service directory in your workspace. Run

```sh
mkdir -p /srv/homeassistant
```

and change to the newly created directory using

```sh
cd /srv/homeassistant
```

Then, open up the `compose.yaml` in an editor using `nano compose.yaml`. Paste the following content:

```yaml
services:
  app:
    container_name: home_assistant
    image: "ghcr.io/home-assistant/home-assistant:stable" # The Image used
    volumes:
      # A new folder where Home Assistant will store its config
      - ./config:/config
      # Allow access to your system time
      # Skip this on macOS and WSL
      - /etc/localtime:/etc/localtime:ro
      # Allow access to dbus, the device control service
      # Skip this on macOS and WSL
      - /run/dbus:/run/dbus:ro
    restart: unless-stopped
    # Give the container more privileges
    # Usually, you shouldn't do this, but Home Assistant
    # needs to access devices like the Bluetooth card
    # for automation purposes
    privileged: true
    # Allow access to the host network for device discovery
    # Usually, you wouln't do this either, but for HA we need it
    # Because we use the host network we do not need to expose container ports
    network_mode: host
```

To start downloading and running HomeAssistant, just run

```sh
docker compose up -d
```

### What does it do?

1. Check if the image (the package containing the service) is already downloaded and downloads it if required
2. Starts the service in background (`-d` stands for detached mode)

Now you should be ready to setup HomeAssistant on port 8123 in your web browser. Sometimes it can take a up to 5 min until the web server is started. Especially if it is the first start and you have older hardware.

- <http://IP:8123> (use the IP you use for connecting via SSH)
- <http://localhost:8123> (if installed locally)

> [!WARNING] HTTP
> We will be using HTTP connections for simplicity, but they are insecure by nature as there is no encryption. Passwords and any other data is transported in plain text and are vulnerable to [man in the middle attacks!](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) . If you are interested about securing the service and exposing it to the internet, talk to us.

For more useful Compose commands, see [[More on Docker]].

> [!WARNING] Backup your data!
> One downside of hosting your own services is that data integrity is not guaranteed. Be sure to always backup your critical data.
