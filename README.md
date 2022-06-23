# televator
Smart 12v motor driver for small to medium sized RV applications (TV lifts, HappyJack bed lift, etc)


### Introduction
This project is meant to connect a standard 12v motor to HomeAssistant through ESPHome. This writeup is based on an RV TV lift (televator), although it can be used for other applications as well.


#### Materials Needed
- NodeMCU ESP32 or ESP8266 ([example](https://www.amazon.com/gp/product/B09J95SMG7/ref=ppx_yo_dt_b_asin_image_o05_s00?ie=UTF8&th=1))
- Jumper wires
- [12v to 5v stepdown](https://www.amazon.com/gp/product/B00CBCGAL8/ref=ppx_yo_dt_b_asin_title_o05_s00?ie=UTF8&th=1) to power the ESP (or an independent power supply)
- [Cytron 20A Bi-Directional 6V-30V DC Motor Driver MD20A](https://www.amazon.com/gp/product/B07RQFN485/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&psc=1) (or any motor driver capable of handling large amp loads)
- HomeAssistant with the ESPHome add-on


### Instructions
***If you have limited experience working with electricity, it is HIGHLY recommended you turn off power to the TV lift before beginning work.

Before starting the project, check your motor to determine it's peak amp usage, as this can affect the motor driver you use. For most small 12v motors, a 20A or 30A board will be sufficient. It is also recommended that you time how long it takes for your TV lift to raise and lower completely. This will come in handy if you want to automate it and don't want to risk burning out your motor. For me, it takes 21 seconds to rise, and 19 seconds to lower.

A typical RV TV lift uses a momentary rocker switch to raise or lower the TV by applying power to the 12v motor (or reversing said power). In most of my projects, I try to retain physical switch control so others can use the device, however that is not the case here. Since the momentary switch reverses the incoming 12v power itself, switch control was not able to be retained without large amounts of rewiring (since my switch is located in the cabinet above the TV). This is easily fixable by adding a wireless smart switch to your setup.

Start by raising your TV and disconnecting your 12v motor from the incoming power supply. Place 3 hole wago connectors on the positive and negative since they may become hot after the next step (depending if you shut off power). Above the TV, disconnect all 4 wires from the back of the rocker switch and connect positive to positive, negative to negative. Essentially you are passing the power from the incoming line to the outgoing line, bypassing the switch.

Below the TV lift, wire the 12v to 5v stepdown into one of the 3 holes of the wago. This will be for powering the ESP32. The 3rd hole will be for supplying power to the motor driver. Add a wire from the 3rd hole to the VB+/VB- on the motor driver board. Connect the 12v motor's input wires to the MA(+) and MB(-). The Cytron board used in this project features an 12v passthrough so the board can change the outgoing 12v polarity depending on what the ESP tells it. There are 3 inputs on the other side of the board (GND, PWR, DIR) for connecting to the ESP. Using jumper wires:
- GND should go to GND on the ESP
- PWR should go to GPIO 26
- DIR should go to GPIO 27.


### HomeAssistant & ESPHome Setup
Regardless of the application, your motor will now be considered a "Cover" in the eyes of HomeAssistant and ESPHome.

Setup your ESPHome and add your new ESP device. Once initial configuration is done, paste the code found in the [ESPHome.yaml](https://github.com/tango2590/televator/blob/9214988cd70cf1514626a7958c143fc4e774a4a8/ESPHome.yml) file beloow "capitave portal:" Under the UP and DOWN sections, you can adjust the amount of time you want the motor to spin in that direction. This is where your timing skills pay off. Change the delay to the respective raising/lowering time you recorded prior to starting this project. Install on the ESP, restart HomeAssistant, and that's it! HomeAssistant will now see a new cover titled Televator that you can use on your dashboard with up/down arrows, or inside NodeRed for automations.

### Automation Ideas
In case you want some automation ideas, here goes. I do everything through NodeRed.
- Harmony Remote (smart remote) turns on the Watch AppleTV activity, which raises the TV and turns on the TV and AppleTV. Activity off turns off and lower the TV.
- Every evening before bed, the TV will lower to encourage us to use the window behind it the next morning.
- If your TV lift has a fireplace below it, use a smart IR blaster or harmony hub to raise and turn on the TV and the fireplace, while lowering the lights in the room.
