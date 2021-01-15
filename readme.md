# DIY Alarm System with Node-Red and Telegram
## Get notations and photos from RPI camera module in Telegram when movement is detected and toggle a siren to chase away intruders!

With Telegram commands via your bot you can toggle the alarm on or off, see it's current status, toggle it off for only a few hours and so on...
I know the flow looks rather confusing at first, but as soon you get the hang of it it will become clear!
Let me know if there are any logical mistakes or errors, I'm sure something's not perfectly thought through. :)

### Hardware needed:
- Raspberry Pi (or other platform to run Node-Red and MQTT-Broker)
- Camera module for RPI (optional)
- ESP8266 (optional)
  - (Code uses libraries "PubSubClient" and "ESP8266WiFi")
- PIR Sensors 
  - For direct connection with RPI - HC-SR501
  - For connection with ESP8266 - AM312
  - see links below
- Jumper cables
- Power Supply for RPI and ESPs
- SONOFF Basic WIFI Wireless Smart Switch with Tasmota (https://tasmota.github.io/docs/devices/Sonoff-Basic/)
- 12V DC Wired Siren Horn
- SONOFF DW1 Door Window Sensor (optional)
- SONOFF RF Bridge WIFI 433 MHz with Tasmota (https://tasmota.github.io/docs/devices/Sonoff-RF-Bridge-433/) 

### Software needed:
- Node-Red (requires NodeJS and npm) --> see https://nodered.org/docs/getting-started/
- MQTT-Broker (for example Mosquitto) --> https://mosquitto.org
- Arduino IDE (to configure ESP modules) --> https://www.arduino.cc/en/software

### Installation:
1. Setup your Raspberry Pi
    - Connect the PIR sensor and camera module to the raspberry
    - raspi-config --> point 5 - "enable camera"
2. Install Node-Red and Mosquitto
    - Node-Red comes preinstalled on some Raspbian Versions
3. Install additional nodes for Node-Red
    - node-red-contrib-fs-ops
    - node-red-contrib-chatbot
    - node-red-contrib-schedex
    - node-red-node-pi-gpio
4. Import the flow from DIY_NR_AlarmSystem.json into Node-Red
5. Create a new telegram Bot via @BotFather on Telegram
6. For your ESP Modules, connect the PIR sensors and flash the code from DIY_ESP_Alarm.txt onto the devices
    - Configure the code to use your WiFi and preferred MQTT Topics
7. Carefully check ALL the nodes in Node-Red and insert required data and tokens etc.
    - Telegram Receiver/Sender needs the Bot token
    - Some function nodes won't work until you specify a chatID in them
    - Configure schedex node to automatically turn the alarm on/off at a given time
    - The fs-ops-move node configures where the taken pictures are saved
    - Conversation nodes need to be specified individually (chatID)
    - Use debug nodes to figure out what exactly is going on if the flow doesn't meet the requierements of your specific setup!
    - Take further steps to configure the flow to fit your specific needs
8. Connect the SONOFF Bridge to your WiFi and check which data the door/windows sensor sends and update the respective function node (right beneath the camera exec node) in Node-Red
9. Wire the siren horn directly to the Sonoff Wifi Switch and set the MQTT topic in Tasmota
    - Configure Node-Red to toggle the siren on or off

### Further information:
Some text nodes still have german text in them, you may configure them to meet your needs. Also, especially used variables are still in german language.
For connecting the PIR sensor to the raspberry directly, we recommend HC-SR501 modules. The SR501 gave us a lot of false positives when connected to an ESp8266 though, which is why we switched to AM312 PIR sensors for those.

- HC-SR501 PIR Sensor: www.banggood.com/HC-SR501-Adjustable-Infrared-IR-Pyroelectric-PIR-Module-Motion-Sensor-Human-Body-Induction-Detector-p-1545488.html
- AM312 PIR Sensor: https://www.banggood.com/HC-SR505-Mini-Infrared-PIR-Motion-Sensor-Precise-Infrared-Detector-Module-p-1011714.html
- RPI Camera Module: https://www.banggood.com/Night-Vision-5-Megapixel-OV5647-Sensor-Camera-Adjustable-focus-Module-With-Infrared-Light-Sensor-for-Raspberry-Pi-4B-or-3B+-or-Zero-p-1478337.html
- SONOFF Basic Wifi Switch: https://www.banggood.com/2Pcs-SONOFF-Basic-10A-2200W-WIFI-Wireless-Smart-Switch-Remote-Control-Socket-APP-Timer-AC90-250V-50-or-60Hz-Works-with-Amazon-Alexa-Google-Home-Assistant-Nest-IFTTT-p-1423314.html
- 12V DC Wired Siren Horn: https://www.banggood.com/Mini-Wired-Siren-For-Wireless-Home-Alarm-Security-System-SZC-2574-p-939525.html
- SONOFF DW1 Door Window Sensor: https://www.banggood.com/5pcs-SONOFF-DW1-433Mhz-Door-Window-Sensor-Compatible-With-RF-Bridge-For-Smart-Home-Alarm-Security-p-1386115.html
- SONOFF RF Bridge Wifi 433Mhz: https://www.banggood.com/SONOFF-RF-Bridge-WIFI-433-MHz-Replacement-Smart-Home-Automation-Universal-Switch-p-1179900.htm

For some reason, I can't make the arduino code send a PIR value of 0 via MQTT to the raspberry, which is why I configured an extra delay node when movement is detected by the ESP modules. IF anyone knows how to send a value of 0 from the ESP8266, please let me know.
There's also a DW2 version of the SONOFF Door/Window Sensor which can connect directly to the WiFi without a bridge, but that would probably mean that it communicates with some SONOFF servers...
For that reason we use the older 433MHz version and Tasmota Firmware on all SONOFF devices, so we know that everything these things send and receive stays where we want it to be! :)
