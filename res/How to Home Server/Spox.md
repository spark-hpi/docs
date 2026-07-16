---
up:
  - "[[06 Going further]]"
hero: spox
---

## What is a Spox?

You want to do Home Assistant automations based on real-time data from your place? Meet the Spox -- a cool, fully extendable sensor box that doesn't cost a fortune or comes in a big and ugly plastic shell?

It reads your room's temperature, humidity, barometric pressure and air quality, tracks up to 3 people across the room with high accuracy, and shows all of it on the box itself.

We built it on top of [Alexander Kutschera's Smart Plants PCB](https://github.com/vektorious/hpi_smart_plants) which he kindly gifted to us for the original workshop.

## So, let's get started!

First, grab your hardware. We designed and 3D-printed our own case, so everything fits properly and looks good on a desk or mounted on a wall. You can find the files for the case inside the [Spox repository](https://github.com/spark-hpi/spox). The PCB can also be found in that repo, but it's not strictly required. For the hardware, see this list:



| Component              | Model                 | Purpose                                                                     |
| ---------------------- | --------------------- | --------------------------------------------------------------------------- |
| Microcontroller        | Seeed Studio ESP32-C6 | The brain of the box. Connects via Wi-Fi and processes the data.            |
| Presence Sensor        | LD2450                | mmWave sensor that can track and triangulate up to 3 targets on a 2D plane. |
| Environment Sensor     | BME680                | Measures temperature, humidity, barometric pressure and VOCs.               |
| Display                | Small OLED            | Displays local readouts in real-time.                                       |
| Anything else YOU want |                       |                                                                             |

If a term or some notation trips you up, jump down to the [[Glossary and Notation]]. It is recommended you glance over the notations before reading the guide.

## Wiring it up

Wire up and test each component on its own before you commit to the full assembly. It's a lot easier to catch a bad solder joint or a wrong pin when there's only one thing on the board. Once each part checks out, bring it all together.

Here's how we hooked everything up to the ESP32-C6:

- OLED display: I²C (3.3V)
- LD2450 (mmWave): UART (5V)
- BME680: I²C (3.3V - 5V)

## The Burn-In Phase

Don't panic if your BME680 air quality numbers look completely off the first time you power it up. The BME680 has a tiny heating element inside for measuring VOCs, and it needs to burn off leftover manufacturing gunk (as well as particles absorbed when unused for an extended period of time) before those readings mean anything.

Leave it running for 24 to 48 hours straight to let it settle. After that, it's good.

## Code and ESPHome

The Spox runs [ESPHome](https://esphome.io/). Instead of writing firmware in C++ by hand, you describe the board and each sensor in one YAML file, and ESPHome compiles that into firmware and flashes it onto the chip. Anything you define shows up in Home Assistant on its own.

You can find a full working configuration inside the [Spox repository](https://github.com/spark-hpi/spox/tree/main/esphome). Edit it and uncomment these lines:

```yaml
# Enable Home Assistant API
api:
  encryption:
    key: "XXXXXX" # replace with the output of the generator at https://esphome.io/components/api/#configuration-variables
ota:
  - platform: esphome

wifi:
  ssid: "Spwifi" # the name of your Wi-Fi
  password: "supersecret!" # and the password

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Frank's Spox"
    password: "supersecret" # change this to something secure

```

If you want to add custom components, see <https://esphome.io>. For laying out what actually shows on the OLED, [Lopaka](https://lopaka.app/) is worth a look. 

To flash this configuration to the device, you need to either install [`esphome` locally](https://esphome.io/guides/installing_esphome/) or set up the [ESPHome Device Builder](https://esphome.io/guides/getting_started_hassio/). Note that the one-click installation is for Home Assistant OS, not the Home Assistant container we set up during the How To Home-Server workshop.

For the device builder:

1. Upload the `paging_dashboard.yaml` to ESPHome.
2. Plug the ESP32-C6 into your computer with a proper data USB cable.
3. Open ESPHome in the browser and download the configuration as a binary (`.hex` file extension)
4. Open <https://web.esphome.io> in a browser that supports WebSerial (currently only Chromium and derivatives), hit **Connect** and pick the board's serial port, then hit **Install**.

For the command line, run inside the `esphome` directory of the Spox repository:

```
esphome run paging_dashboard.yaml
```

Note that the device name is derived from the configuration file name, so you might want to rename the file to something like `spox.yaml`. You do not have to rename the `paging_dashboard.h`. 

After the first flash you can ditch the cable. Every change to the YAML after that gets pushed over the air. All that remains is to add your device to Home Assistant. From the ESPHome documentation:

> Manually add the device on the Home Assistant Integrations page. To do so, click on the "Add Integration" button (bottom right), search for "ESPHome" and enter the ESPHome device's host name. The host name is based on the name you’ve given to the device; if you named your device "living-room-lamp", its host name will be living-room-lamp.local. You can also enter the device’s IP address, if for some reason you prefer to use that.

## Building automations

Once the ESP32-C6 is giving you solid readings, it's time to get that data into Home Assistant.

Pulling in numbers is the easy part. The fun starts when you do something with them. Because the LD2450 knows roughly where you are in the room, you can build automations that are way cooler than basic motion sensors.

1. Proximity lighting: Step toward your desk and the light ramps up. Step away and it fades back down.
2. Bedtime mode: Get into bed and Home Assistant kicks on your night light and powers down the rest of the room.
3. Instant presence: Walk in and the lights come on right away. Walk out and they reliably shut off.

## Congrats, you now have a Spox!

There's a lot more you can do with it, and we'd love to hear what you come up with.

Thanks for building along. If there's an automation you want to see next, tell us, or better yet, write it yourself and show us!
