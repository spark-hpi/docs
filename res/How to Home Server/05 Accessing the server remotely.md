---
up:
  - "[[How To Home Server]]"
---
Now, you have a service running. You can do all the Home Assistant things you want. But what happens if you are on the go and want to access Home Assistant? Or if you have set up a different service and need to access it from the go? You could expose the service to the internet, but that's a hassle and also might be dangerous.

Instead, we will use [NetBird](https://netbird.io), a free and open-source German-made mesh VPN, which will allow all devices connected to the mesh to talk to each other. The idea is simple: install NetBird on the server and on your laptop, then you can connect to your server from anywhere. For the purpose of this guide, we will use the forever-free tier of their cloud service, but you can always host your own control server. In the future, we might offer a Spark NetBird server, run by us, potentially on HPI servers.

Begin by going to https://app.netbird.io and creating an account. When prompted, select "peer-to-peer network" as the means of operation. Then, follow the instructions to connect your laptop. When prompted to connect your second device, click "Install with a setup key" and then Continue. 

You will see two commands, like

```sh
curl -fsSL https://pkgs.netbird.io/install.sh | sh
```

and 

```sh
netbird up --setup-key <SETUP-KEY>
```

Log into your server via SSH and run them. Once connected, go back to the onboarding in your browser. You will see a command like `ping <IP>`. Copy only the IP, then run, on your laptop:

```sh
ssh root@<IP>
```

This should connect you to your server. When on the go, you can run this command instead of the `ssh <NAME>` you've been using to access your server remotely. This goes for any service, not just SSH -- try accessing Home Assistant at `http://<IP>:8123`.