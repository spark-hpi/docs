---
up:
  -
  - "[[01 Choosing an OS]]"
---

Raspberry Pi uses its own optimised version of Debian and offers a separate installation tool.

## Raspberry Pi Imager

You can download [the Raspberry Pi Imager](https://www.raspberrypi.com/software/). If you have problems with running the imager, you can download [an older, more stable version](https://github.com/raspberrypi/rpi-imager/releases/tag/v1.9.6). The older version might differ from this guide but the main steps should be similar.

To flash an SD Card with Debian, do the following:

1. Select your Raspberry Pi device and click on "OS"
2. Scroll down and select "Raspberry Pi OS (other)". Now select "Raspberry Pi OS Lite (64-bit)". since we don't want a resource intensive user interface.
3. On the "Storage" tab choose your SD Card to write to.
4. Now we can configure some settings even before installation, click on "Customisation"
   - Hostname: The name that is used to connect to web interfaces and other services running on your Raspberry Pi. Please **choose a unique name** (for example, `<YOURNAME>-server`) . It can be **changed later**.
   - Localisation:
     - Supply information on your current region (e.g. Berlin)
     - Select your preferred keyboard layout
   - User:
     - You will be using the internal root user when configuring the system, but it is mandatory to create a separate non-root user. Set it up with whatever, it doesn't matter.
   - Wi-Fi:
     - You can setup your home wifi if you don't have a LAN connection at home. (We recommend using a setup with a LAN Cable for more stable connections)
   - Remote access:
     - Enable SSH
     - We recommend using "public key authentication" for a more secure connection and less password prompts :)
       If you already have an SSH key, add it here, if not, we will set it up later.
   - Raspberry Pi Connect:
     - We will use SSH instead (leave disabled)
5. Write the Image to the SD Card. **WARNING:** The SD Card will be formatted. All data on it will be lost!
6. Wait until writing is done.

## Starting Raspberry Pi OS

Ensure the Raspberry Pi is powered off. Now insert the SD Cart into the SD Card slot on the back of the Pi. Supply the Pi with power. It should start on its own and extract and install Raspberry Pi OS on the SD Card. This can take up to 5 min and a few reboots.
Check the connection to the Raspberry with `ssh <HOSTNAME>`. If connection is successful you are ready to continue with [[03 Installing Docker |Installing Docker]].

## Dedicated hard drive

If you have a dedicated hard drive available, we strongly recommend using for storing data. SD cards have bad write endurance and can break over time. Other drives are faster and have a longer lifespan.
