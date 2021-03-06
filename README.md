# ESP8266_INTEX_SPA

This is a firmware for ESP8266 to expose a INTEX SPA to a domotic system (Domoticz, jeedom...).

Due to the high power consumption of the heater, it is needed to keep it off as much as possible to optimize electricity bill and save our planet.

As a 1st step, I aimed to start heating the SPA while not at home, thanks to a domotic server. 

I'm personally using domoticz, which will be my test system. But this firmware shall be agnostic as based on HTTP requests.

The original PCB of the SPA must be hacked : Add relays to control switches, external DS18B20 for temperature feedback, get status from original PCB outputs.

One of the targets of my project was to keep the existing electronics in order to :
* keep it safe : galvanic insulation, overheating protection, pump flow control...
* keep the existing control panel

## Hacking the INTEX SPA
DISCLAIMER : of course, the warranty would void after you do this hack. 
I can not be responsible for any trouble of damage caused by those instruction.

WARNING : There is AC voltage on boards, don't do anything unless you know what you are doing !

WARNING1 : Never ever ever power this circuit directly without a GFCI outlet (differential circuit breaker). Remember this circuit will be used in a wet environnement. If you don't understand this, please watch this video https://www.youtube.com/watch?v=SHGo-52wCDc&t=63s and get some electrical knowledge about ground protection.

WARNING2 : As water and an outside use (rain, snow, wind...) are involved with a SPA, take care of the water tightness of your wiring !

### Switches board hack :
Add 4 wires to buttons PWR and HEATER.
![Switches](/Hack_Pictures/IMG_20191215_160735.jpg)
*Yep, I also added a drop of hot glue on the buzzer to have it quieter*

### Main board hack
Solder 3 wires to get Water pump, heater and jet pump states on R3, R4 & R5 (microcontroller side).
![MainBoardWires](/Hack_Pictures/StateOuts.png)

5V DC supply is taken directy from the board headers
![PowerSupply](/Hack_Pictures/5VPowerSupply.png)


### Add your own electronics
* 1x ESP8266 Wemos D1 mini
* 1x Dual relay PCB
* 1x DS18B20 temperature sensor
* 1x Quad level converter (SPA is 5V, but ESP needs 3.3V)

All the above were attached with jumper wires and glued to the Main board cover.
![PowerSupply](/Hack_Pictures/AddElectronics.pptx.png)

## Usage
### HTML REQUESTS

    http://<ESP8266 IP> --> Will provide system overview

    http://<ESP8266 IP>/power --> Will push the ON/OFF button for 500ms

    http://<ESP8266 IP>/heat --> Will push the Heater button for 500ms
*NOTE : Actually, no check of the power state before triggering "heat", will provide no result except relay click if used*

### JSON status

    http://<ESP8266 IP>/status --> Will provide main board output status under JSON format


## Domoticz integration
See [Domoticz](/Domoticz)

## TODO, if need comfirmed by the tests
* estimate time for heating the SPA
* set a timecode when the spa must be ready and start it at the right time
* Implement current sensor to get the power consumption

## References:
* @ripleyXLR8 reverse engineering of the main board (working principles and wiring), Thanks !
https://github.com/ripleyXLR8/intex-SSP-H-20-1-ESP32_spa_manager
* Found somebody whom reverse engineered the control board signal through 74HC595 shift registers, but unable to retrieve link...

Suitable to retrieve 7-segments display, but not to issue commands (display & buttons are muxed and some kind of scan sequence is issued by the main board after a single pin state change).

* [FR] general dicussion about attaching ESP to the intex SPA
https://www.jeedom.com/forum/viewtopic.php?t=19832
