---
up:
  - "[[06 Going further]]"
hero: spox
---

## What is a Spox?

You want to do Home Assistant automations based on real-time data from your place? Meet the Spox -- a cool, fully extendable sensor box that doesn't cost a fortune or comes in a big and ugly plastic shell?

It reads your room's temperature, humidity, barometric pressure and air quality, tracks up to 3 people across the room with high accuracy, and shows all of it on the box itself.

We built it on top of [Alexander Kutschera's Smart Plants PCB](https://github.com/vektorious/hpi_smart_plants) which he kindly gifted to us for the original workshop.

## What are we going to deploy?

This is part two of our Smart Home series. We're building the Spox from scratch: a few sensors and a small OLED wired to an ESP32, all sitting in a case we designed ourselves.

The ESP32 reads the sensors, the screen shows you what's going on at a glance, and once it's running we pull everything into Home Assistant so you can actually automate your room.

## So, let's get started!

First, grab your hardware. We designed and 3D-printed our own case, so everything fits properly and looks good on a desk or mounted on a wall.

If a term or some notation trips you up, jump down to the [[Glossary and Notation]]. It is recommended you glance over the notations before reading the guide.

## The Hardware

Here's everything that goes into a Spox:

| Component              | Model                 | Purpose                                                                     |
| ---------------------- | --------------------- | --------------------------------------------------------------------------- |
| Microcontroller        | Seeed Studio ESP32-C6 | The brain of the box. Connects via Wi-Fi and processes the data.            |
| Presence Sensor        | LD2450                | mmWave sensor that can track and triangulate up to 3 targets on a 2D plane. |
| Environment Sensor     | BME680                | Measures temperature, humidity, barometric pressure and VOCs.               |
| Display                | Small OLED            | Displays local readouts in real-time.                                       |
| Case                   | Custom 3D Print       | Houses all the components securely.                                         |
| Anything else YOU want |                       |                                                                             |

## Wiring it up

Wire up and test each component on its own before you commit to the full assembly. It's a lot easier to catch a bad solder joint or a wrong pin when there's only one thing on the board. Once each part checks out, bring it all together.

Here's how we hooked everything up to the ESP32-C6:

- OLED display: I²C (3.3V)
- LD2450 (mmWave): UART (5V)
- BME680: I²C (3.3V - 5V)

## The Burn-In Phase

Don't panic if your BME680 air quality numbers look completely off the first time you power it up. The BME680 has a tiny heating element inside for measuring VOCs, and it needs to burn off leftover manufacturing gunk before those readings mean anything.

Leave it running for 24 to 48 hours straight to let it settle. After that, it's good.

## The Display

For laying out what actually shows on the OLED, [Lopaka](https://lopaka.app/) is worth a look. It's a browser-based editor where you drag text, icons and shapes onto a 1:1 preview of your screen, then export the drawing code straight into your ESPHome display lambda. Beats nudging pixel offsets by hand.

## Code and ESPHome

The Spox runs on [ESPHome](https://esphome.io/). Instead of writing firmware in C++ by hand, you describe the board and each sensor in one YAML file, and ESPHome compiles that into firmware and flashes it onto the chip. Anything you define shows up in Home Assistant on its own, so there's no glue code to maintain.

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

**Flashing, the quick way:**

1. Plug the ESP32-C6 into your computer with a proper data USB cable, not a charge-only one.
2. Open [web.esphome.io](https://web.esphome.io) in a browser that supports WebSerial (Chrome or Edge), hit **Connect** and pick the board's serial port.
3. Hit install. Once it joins your Wi-Fi it shows up in Home Assistant.

After the first flash you can ditch the cable. Every change to the YAML after that gets pushed over the air.

Running Home Assistant OS? Install the **ESPHome Device Builder** add-on and keep all your configs in there instead. Same idea, managed from inside HA.

Prefer the terminal? With ESPHome installed locally, `esphome run spox.yaml` compiles your config, flashes it over USB (or over the air if the board is already on the network) and tails the logs so you can watch it boot.

## Integrating with Home Assistant

Once the ESP32-C6 is giving you solid readings, it's time to get that data into Home Assistant.

Pulling in numbers is the easy part. The fun starts when you do something with them. Because the LD2450 knows roughly where you are in the room, you can build automations that are way cooler than basic motion sensors.

1. Proximity lighting: Step toward your desk and the light ramps up. Step away and it fades back down.
2. Bedtime mode: Get into bed and Home Assistant kicks on your night light and powers down the rest of the room.
3. Instant presence: Walk in and the lights come on right away. Walk out and they reliably shut off.

## Congrats, you now have a Spox!

There's a lot more you can do with it, and we'd love to hear what you come up with.

Thanks for building along. If there's an automation you want to see next, tell us, or better yet, write it yourself and show us!
