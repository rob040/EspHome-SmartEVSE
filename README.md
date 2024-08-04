# EspHome-SmartEVSE
ESPhome yaml scripts for sending DSMR-5 Smart Meter P1-port data to SmartEVSE


## Intro
The OpenSource [SmartEVSE](https://githuhttps://github.com/SmartEVSE/SmartEVSE-3/blob/master/README.md) (Smart Electric Vehicle Charging Supply Equipment controller) has the ability to charge car with surplus solar energy, and the ability to watch for utility supply over current situations, by telling the in-car AC charger how much current is available. For this it does need Current (A) information from the utility power meter. In the Netherlands there is the DSMR-5 standard for that. The utility mains power meter P1 port, optical-isolated from mains, supplies data for that. The [ESPhome DSMR](https://esphome.io/components/sensor/dsmr.html) component provides for the reading, buffering, decoding of DSMR data. The here provided scripts provide the processing and sending the required information (MainsMeter) to the SmartEVSE equipment.

There are two different ways of sending MainsMeter data to SmartEVSE:
1. via REST-API http POST command (available here)
2. via MQTT SmartEVSE Set/MainsMeter topic (available here)

   The MQTT route is said to get faster reaction time in SmartEVSE software (i've not measured that), but it does require extra equipment: an MQTT server. A simple setup is commonly an Raspberry-Pi with Home Assistant and Mosquitto AddOn.
   The REST-API route is directer as it does not require Home Assistant server to be up and running, and thus is more robust.
   Both still require WiFi connection to be operable though.
   An WiFi-free wired solution is the [Stegen.com sensorbox V2 (€59)](https://www.stegen.com/en/ev-products/129-smart-evse-sensorbox-v2.html), or an ESP(-home) DSMR to MODBUS gateway that mimics the sensorbox. The latter is future work.

## Hardware
For ESP hardware, I use the very cheap ESP32-C3-mini (RISC-V) modules. Also the ESP32 D1 mini (based on Lolin Wemos D1 mini with old ESP8266).
Instead of an ESP32, the older lower performing ESP8266 will be possible as well, as they all are supported by ESPhome and PlatformIO, but has not been tested.

[ESP32-C3-mini](/images/esp32c3-supermini-pcb.png)
_The ESP32-C3-mini module._

[ESP32 D1 mini](/images/ESP32-04-D1Mini.webp)
_The ESP32 D1 mini module._

On ESP32-C3-mini board, only the Ground, +5V and RX are connected to the P1-port inverter.
[Connection diagram](/images/dsmr-water-esp32-c3-schema.png)

### note:
When using ESP boards that have USB to serial chip on board (like the D1 mini), one must be carefull about what is connected to the RX line; The USB to serial chip must have series resistor AND the P1-port inverter must be silenced when programming the ESP via USB serial port. 
Alternatively, a removeable jumper, or alternate UART port pins may need to be used for P1 data input.

## Software
[ESPhome](https://esphome.io/) is needed to program the ESP32 chip, with the here provided yaml scripts as input. It can be run from within [Home Assistant](https://esphome.io/guides/getting_started_hassio) or as a [Python add-on on a PC](https://esphome.io/guides/getting_started_command_line). See the [ESPhome](https://esphome.io/) __Getting Started__ pages. For me, the compilation from PC commandline is much faster (at most 30s) than from within HomeAssistant (15 minutes on Rpi-3B).

On PC, the commandline to compile and (USB or OTA) download is as simple as:
  `esphome run dsmr-mqtt-espc3.yaml`

### secrets
ESPhome (and Home Assistant) use [secret.yaml](https://esphome.io/guides/faq.html) files to to contain site (user-) specific entries. A template is included. You should copy the [secret-template.yaml](/secrets-template.yaml) to [secret.yaml](/secrets.yaml) and modify each line the '<' and '>' entries. Then copy the secret.yaml to each sub-directory with yaml files.


